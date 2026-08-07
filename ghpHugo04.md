---
title: 第四课：GitHub Actions 深度定制
---

# 第四课：GitHub Actions 深度定制 —— 掌控自动化部署的每一个细节

**教学理念**：前三课你学会了*搭建*、*配置*和*写作*。这一课，我们来揭开自动化部署的*黑盒*——理解 GitHub Actions 的每一个环节，并学会*定制*它。通过“观察→修改→实验→理解”的循环，你将真正掌握 CI/CD 的核心思想。

---

## 本课目标

完成本课后，你将能够：
- 理解 GitHub Actions 的核心概念（Workflow、Job、Step、Action、Runner）
- 读懂并修改 `deploy.yml` 工作流文件
- 添加手动触发（`workflow_dispatch`）功能
- 使用缓存（Cache）加速构建
- 配置环境变量和 Secrets
- 添加自定义域名
- 理解主题自动更新机制

---

## 第一部分：GitHub Actions 核心概念（3 分钟）

在动手之前，先建立一张“概念地图”。

### 1.1 五个核心概念

| 概念 | 含义 | 类比 |
|------|------|------|
| **Workflow**（工作流） | 一个完整的自动化流程，定义在 `.yml` 文件中 | 一份“施工蓝图” |
| **Event**（事件） | 触发 Workflow 运行的条件 | “开工信号” |
| **Job**（作业） | Workflow 中的一个任务单元，可并行或串行 | 蓝图中的一个“施工阶段” |
| **Step**（步骤） | Job 中的最小执行单元（一个命令或一个 Action） | 阶段中的“具体操作” |
| **Action**（动作） | 可复用的 Step 封装（由社区或官方提供） | “预制构件” |
| **Runner**（运行器） | 执行 Workflow 的虚拟机 | “施工队” |

### 1.2 查看你的 Workflow

```bash
cat .github/workflows/deploy.yml
```

这就是你博客的“施工蓝图”。接下来，我们逐行拆解它。

---

## 第二部分：逐行拆解 `deploy.yml`（10 分钟）

### 2.1 Workflow 名称与事件触发

```yaml
name: Build and deploy
on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:    # 允许手动触发
```

| 部分 | 作用 |
|------|------|
| `name` | Workflow 的名称，会显示在 GitHub Actions 界面 |
| `on: push` | 当代码被推送到 `main` 或 `master` 分支时自动触发 |
| `workflow_dispatch` | 允许你在 GitHub Actions 界面**手动点击按钮**触发运行 |

> **🧪 实验 1**：去你的 GitHub 仓库，点击 **Actions** 选项卡，找到 "Build and deploy" 工作流，点击 **“Run workflow”** 按钮，选择 `main` 分支，点击运行。观察：即使没有推送代码，工作流也会执行。

### 2.2 权限设置

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

| 权限 | 作用 |
|------|------|
| `contents: read` | 读取仓库代码 |
| `pages: write` | 写入 GitHub Pages（部署权限） |
| `id-token: write` | 用于 OIDC 身份认证（安全部署） |

> **关键洞察**：这些权限是**最小权限原则**的体现——只给 Workflow 完成任务所需的最少权限。

### 2.3 环境变量 —— 版本锁定

```yaml
env:
  DART_SASS_VERSION: 1.97.1
  GO_VERSION: 1.25.5
  HUGO_VERSION: 0.154.2
  NODE_VERSION: 24.12.0
```

这些环境变量**锁定了**构建工具的版本。

> **为什么需要锁定版本？** 如果使用 `latest`，哪天工具更新了，你的构建可能突然失败。锁定版本确保**可复现的构建**。

> **🧪 实验 2**：把 `HUGO_VERSION` 改成 `0.120.0`（一个较老版本），推送代码，观察 Actions 是否失败。然后改回来。

### 2.4 Job 定义 —— Build（构建）

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v6
```

| 部分 | 作用 |
|------|------|
| `jobs:` | 定义所有 Job |
| `build:` | Job 的名称 |
| `runs-on: ubuntu-latest` | 在最新的 Ubuntu 虚拟机上运行 |
| `actions/checkout@v6` | 将你的代码拉取到 Runner 上 |

> **关键洞察**：每次 Workflow 运行，GitHub 都会**启动一台全新的虚拟机**，在上面执行所有步骤。运行结束后，虚拟机被销毁。这就是“无状态”的 CI/CD 哲学。

### 2.5 设置 Go
{% raw %}
```yaml
- name: Setup Go
  uses: actions/setup-go@v6
  with:
    go-version: ${{ env.GO_VERSION }}
```

`actions/setup-go` 在 Runner 上安装指定版本的 Go。`${{ env.GO_VERSION }}` 引用了前面定义的环境变量。
{% endraw %}
### 2.6 设置 Hugo
{% raw %}
```yaml
- name: Setup Hugo
  uses: peaceiris/actions-hugo@v3
  with:
    hugo-version: ${{ env.HUGO_VERSION }}
    extended: true
```
{% endraw %}
`peaceiris/actions-hugo` 是一个社区 Action，用于安装 Hugo。

> **注意**：`extended: true` 表示安装 Hugo 的**扩展版**。Stack 主题 v4 需要 Hugo 0.157.0 以上的扩展版。

### 2.7 构建网站
{% raw %}
```yaml
- name: Build the site
  run: |
    hugo \
      --gc \
      --minify \
      --baseURL "${{ steps.pages.outputs.base_url }}/" \
      --cacheDir "${{ runner.temp }}/hugo_cache"
```
{% endraw %}
| 参数 | 作用 |
|------|------|
| `--gc` | 垃圾回收，清理无用缓存 |
| `--minify` | 压缩生成的 HTML/CSS/JS |
| `--baseURL` | 动态设置网站根地址 |
| `--cacheDir` | 指定缓存目录 |

> **关键洞察**：`hugo` 命令将 `content/` 下的 Markdown 文件 + `config/` 下的配置 + 主题模板，编译成 `public/` 目录下的静态 HTML 文件。

### 2.8 上传构建产物

```yaml
- name: Upload artifact
  uses: actions/upload-pages-artifact@v5
  with:
    path: ./public
```

`actions/upload-pages-artifact` 将 `public/` 目录打包成一个 **artifact**（构建产物），供后续部署使用。

### 2.9 Deploy（部署）Job
{% raw %}
```yaml
deploy:
  environment:
    name: github-pages
    url: ${{ steps.deployment.outputs.page_url }}
  runs-on: ubuntu-latest
  needs: build
  steps:
    - name: Deploy to GitHub Pages
      id: deployment
      uses: actions/deploy-pages@v5
```
{% endraw %}
| 部分 | 作用 |
|------|------|
| `needs: build` | 等待 `build` Job 完成后才执行 |
| `environment: github-pages` | 部署到 GitHub Pages 环境 |
| `actions/deploy-pages@v5` | GitHub 官方的 Pages 部署 Action |

> **关键洞察**：`build` 和 `deploy` 是**分离**的。先构建，再部署。如果构建失败，部署不会执行。

---

## 第三部分：实验课 —— 修改 Workflow（10 分钟）

### 🧪 实验 3：添加手动触发输入参数

`workflow_dispatch` 支持自定义输入参数，让你在手动触发时可以选择行为。

**操作**：编辑 `.github/workflows/deploy.yml`，在 `workflow_dispatch:` 下添加：

```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:
    inputs:
      hugo_version:
        description: 'Hugo 版本（留空使用默认）'
        required: false
        default: ''
        type: string
      skip_deploy:
        description: '跳过部署（仅构建）'
        required: false
        default: false
        type: boolean
```

然后修改 `Setup Hugo` 步骤，支持动态版本：
{% raw %}
```yaml
- name: Setup Hugo
  uses: peaceiris/actions-hugo@v3
  with:
    hugo-version: ${{ github.event.inputs.hugo_version || env.HUGO_VERSION }}
    extended: true
```
{% endraw %}
修改部署步骤，支持跳过部署：

```yaml
- name: Deploy to GitHub Pages
  if: github.event.inputs.skip_deploy != 'true'
  uses: actions/deploy-pages@v5
```

**提交并测试**：

```bash
git add .
git commit -m "feat: 添加手动触发参数"
git push origin main
```

去 Actions 选项卡，点击 "Run workflow"，你会看到输入框！尝试：
1. 输入不同的 Hugo 版本（如 `0.160.0`）
2. 勾选 "Skip deploy"

### 🧪 实验 4：添加缓存加速构建

每次构建都重新下载所有依赖很慢。使用缓存可以大幅加速。

**操作**：在 `deploy.yml` 的 `build` Job 中，在 `Setup Hugo` 之后添加：
{% raw %}
```yaml
- name: Cache Hugo resources
  uses: actions/cache@v5
  with:
    path: |
      resources/_gen
      ${{ runner.temp }}/hugo_cache
    key: ${{ runner.os }}-hugo-resources-${{ hashFiles('**/go.sum') }}
    restore-keys: |
      ${{ runner.os }}-hugo-resources-
```
{% endraw %}
**解释**：
- `path`：要缓存的目录（Hugo 生成的资源和构建缓存）
- `key`：缓存的唯一标识。当 `go.sum` 变化时，缓存会失效并重建
- `restore-keys`：如果没有精确匹配的缓存，用前缀匹配恢复

**提交并观察**：

```bash
git add .
git commit -m "perf: 添加 Hugo 缓存加速构建"
git push origin main
```

第一次运行会**填充缓存**（耗时较长），第二次及以后的运行会**命中缓存**（大幅加速）。对比两次的运行时间！

### 🧪 实验 5：添加条件触发 —— 只在特定文件变化时构建

如果你的博客有很多非内容文件（如 README、.gitignore），每次推送都会触发构建，浪费资源。

**操作**：修改 `on: push` 部分：

```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'content/**'
      - 'config/**'
      - 'assets/**'
      - 'layouts/**'
      - 'go.mod'
      - 'go.sum'
      - '.github/workflows/deploy.yml'
```

现在，只有当你修改了 `content/`、`config/`、`assets/` 等目录下的文件时，才会触发构建。

> **🧪 实验**：修改 README.md 并推送，观察 Actions **是否**被触发。然后修改一篇博客文章并推送，观察 Actions **是否**被触发。

---

## 第四部分：环境变量与 Secrets（5 分钟）

### 4.1 环境变量（Environment Variables）

环境变量用于在 Workflow 中传递配置值。

**两种设置方式**：

| 方式 | 位置 | 用途 |
|------|------|------|
| `env:` | Workflow 文件内 | 公开的配置（如版本号） |
| Repository Secrets | GitHub 仓库 Settings → Secrets | 敏感信息（如 Token） |

### 4.2 Secrets —— 安全存储敏感信息

GitHub Secrets 是**加密存储**的变量，在日志中会被自动隐藏。

**创建 Secret**：
1. 仓库 → **Settings** → **Secrets and variables** → **Actions**
2. 点击 **New repository secret**
3. 名称：`CUSTOM_DOMAIN`，值：`your-domain.com`

**在 Workflow 中使用**：
{% raw %}
```yaml
- name: Create CNAME file
  run: echo "${{ secrets.CUSTOM_DOMAIN }}" > public/CNAME
```
{% endraw %}
### 4.3 GITHUB_TOKEN —— 自动生成的 Token

GitHub Actions 会自动生成一个 `GITHUB_TOKEN`，用于认证当前 Workflow。
{% raw %}
```yaml
with:
  github_token: ${{ secrets.GITHUB_TOKEN }}
```
{% endraw %}
> **关键洞察**：`GITHUB_TOKEN` **不需要**你手动创建。它的权限由 Workflow 的 `permissions` 字段控制。

---

## 第五部分：自定义域名（5 分钟）

你的博客现在在 `https://<用户名>.github.io`。如果你想用自己的域名（如 `blog.yourname.com`），可以这样配置。

### 5.1 方法一：通过 GitHub Settings 设置（推荐）

1. 仓库 → **Settings** → **Pages**
2. 在 **Custom domain** 输入框中填入你的域名
3. 点击 **Save**
4. 在域名服务商处添加 CNAME 记录，指向 `<你的用户名>.github.io`

> **注意**：如果你使用 GitHub Actions 部署，GitHub **不会自动创建** CNAME 文件。你需要手动在 `public/` 目录下创建。

### 5.2 方法二：通过 Workflow 自动创建 CNAME 文件

在 `deploy.yml` 的 `Build the site` 步骤之后，添加：

```yaml
- name: Create CNAME file
  run: echo "blog.yourname.com" > public/CNAME
```

或者使用 Secret：
{% raw %}
```yaml
- name: Create CNAME file
  run: echo "${{ secrets.CUSTOM_DOMAIN }}" > public/CNAME
```
{% endraw %}
### 5.3 在配置文件中设置 baseurl

别忘了在 `config/_default/config.toml` 中更新：

```toml
baseurl = "https://blog.yourname.com/"
```

> **🧪 实验**：如果你有一个域名，尝试配置自定义域名。如果没有，可以跳过，继续使用 `username.github.io`。

---

## 第六部分：主题自动更新机制（3 分钟）

你的模板中包含另一个 Workflow：`update-theme.yml`。

```bash
cat .github/workflows/update-theme.yml
```

```yaml
name: Update theme
on:
  schedule:
    - cron: "0 0 * * *"    # 每天 UTC 0 点自动运行
  workflow_dispatch:       # 也支持手动触发

jobs:
  update-theme:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v6
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
      - name: Update theme
        run: hugo mod get -u
      - name: Tidy go.mod, go.sum
        run: hugo mod tidy
      - name: Commit changes
        uses: stefanzweifel/git-auto-commit-action@v7
        with:
          commit_message: "CI: Update theme"
```

**工作原理**：

1. **每天 UTC 0 点**（北京时间早上 8 点），GitHub 自动触发这个 Workflow
2. 它运行 `hugo mod get -u`，拉取 Stack 主题的最新版本
3. 运行 `hugo mod tidy`，更新 `go.mod` 和 `go.sum`
4. 如果有更新，自动提交并推送回你的仓库
5. 推送会**触发** `deploy.yml`，自动重新构建和部署

> **关键洞察**：这是一个**全自动的依赖更新流水线**。你的博客主题始终保持最新，而你完全不需要手动干预。

> **🧪 实验**：手动触发 `update-theme` Workflow（点击 Actions → Update theme → Run workflow），观察它是否更新了 `go.mod` 和 `go.sum`。

---

## 第七部分：Workflow 调试技巧（2 分钟）

### 7.1 查看日志

1. 仓库 → **Actions** → 点击某个运行记录
2. 点击左侧的 Job 名称（如 `build` 或 `deploy`）
3. 展开每个 Step 查看详细输出

### 7.2 添加调试输出

在 Workflow 中添加 `echo` 命令输出变量值：
{% raw %}
```yaml
- name: Debug info
  run: |
    echo "Hugo version: ${{ env.HUGO_VERSION }}"
    echo "Runner OS: ${{ runner.os }}"
    echo "Event name: ${{ github.event_name }}"
```
{% endraw %}
### 7.3 使用 `ACTIONS_STEP_DEBUG` 开启详细日志

在仓库 Settings → Secrets → Actions 中添加一个 Secret：
- 名称：`ACTIONS_STEP_DEBUG`
- 值：`true`

之后运行 Workflow，每个 Step 会输出更详细的调试信息。

### 7.4 常见错误排查

| 错误 | 可能原因 | 解决方案 |
|------|---------|---------|
| `hugo: command not found` | Hugo 未正确安装 | 检查 `actions/setup-hugo` 步骤 |
| `baseURL mismatch` | `baseurl` 配置错误 | 检查 `config.toml` 和 `--baseURL` 参数 |
| `permission denied` | 权限不足 | 检查 `permissions` 字段 |
| `cache not found` | 缓存未命中 | 检查 `cache` 步骤的 `key` 是否正确 |

---

## 本课总结

| 概念 | 你做了什么 | 你学到了什么 |
|------|-----------|-------------|
| **Workflow 结构** | 逐行阅读了 `deploy.yml` | Workflow = 事件 + Job + Step + Action |
| **手动触发** | 添加了 `workflow_dispatch` 输入参数 | 可以带参数手动触发 Workflow |
| **缓存** | 添加了 `actions/cache` | 缓存依赖可大幅加速构建 |
| **条件触发** | 添加了 `paths` 过滤 | 只在相关文件变化时才构建 |
| **自定义域名** | 配置了 CNAME 文件 | GitHub Pages 支持自定义域名 |
| **主题自动更新** | 理解了 `update-theme.yml` | 定时任务自动更新主题依赖 |
| **调试** | 学习了日志查看和调试技巧 | 知道如何排查 Workflow 问题 |

---

## 你的家庭作业

1. **添加一个自定义 Step**：在 `deploy.yml` 的 `Build the site` 之后，添加一个 Step 输出构建时间：
   ```yaml
   - name: Show build stats
     run: |
       echo "Build completed at $(date)"
       du -sh public/
   ```

2. **配置缓存**：按照实验 4 的步骤，为你的博客添加缓存，对比启用缓存前后的构建时间

3. **（挑战）添加预览部署**：为 Pull Request 添加预览部署功能，每次 PR 自动构建并部署到一个预览 URL

4. **（挑战）配置自定义域名**：如果你有域名，按照第五部分的步骤配置

5. **阅读官方文档**：浏览 [GitHub Actions 官方文档](https://docs.github.com/actions) 和 [Hugo 部署文档](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

---

*“自动化是工程师的杠杆。你花 10 分钟写一个 Workflow，未来每一分钟都在受益。”*
