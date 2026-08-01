# 第二课：探索配置文件 —— 让博客真正属于你

**教学理念**：延续“自上而下”的探究式学习。第一课你看到了*结果*（一个能跑的博客），第二课我们来*探索*这个结果是如何被控制的。通过“破坏→修复→理解”的循环，你将在实验中掌握 Hugo 配置系统的核心。

---

## 本课目标

完成本课后，你将能够：
- 理解 Hugo 配置文件的层次结构与作用
- 自定义博客的标题、侧边栏、菜单、社交链接
- 通过“制造错误”来理解配置项之间的依赖关系
- 掌握 Stack 主题的核心配置范式

---

## 第一部分：认识你的博客文件结构（3 分钟）

首先，让我们看看这个模板项目的完整文件树。在你的博客根目录下运行：

```bash
tree -L 3
```

你应该看到类似下面的结构（基于 2026 年 5 月最新模板）：

```
.
├── .devcontainer/          # GitHub Codespaces 开发容器配置
├── .github/
│   └── workflows/
│       ├── deploy.yml      # 主部署工作流
│       └── update-theme.yml # 每日自动更新主题
├── .vscode/                # VSCode 编辑器推荐配置
├── assets/
│   ├── img/                # 图片资源（头像、favicon等）
│   └── scss/               # 自定义样式文件
├── config/
│   └── _default/           # ⭐ 核心配置目录（我们重点关注）
│       ├── _languages.toml # 多语言配置
│       ├── config.toml     # 站点基础配置
│       ├── markup.toml     # Markdown 渲染配置
│       ├── menu.toml       # 导航菜单配置
│       ├── module.toml     # Hugo 模块配置（主题引用）
│       ├── params.toml     # ⭐ Stack 主题专属参数
│       ├── permalinks.toml # URL 永久链接格式
│       └── related.toml    # 相关文章配置
├── content/
│   ├── _index.md           # 首页内容
│   ├── categories/         # 分类页面
│   ├── page/               # 独立页面（关于、友链等）
│   └── post/               # ⭐ 博客文章存放处
├── go.mod                  # Go 模块依赖定义
└── go.sum                  # Go 模块依赖校验
```

> **关键洞察**：`config/_default/` 目录下的所有 `.toml` 文件共同定义了你的博客如何工作、如何呈现。Hugo 会在构建时自动读取这个目录下的所有配置文件。

---

## 第二部分：逐个击破 —— 每个配置文件的作用（10 分钟）

### 2.1 `config.toml` —— 博客的“身份证”

这是 Hugo 最基础的配置文件。打开它看看：

```bash
cat config/_default/config.toml
```

```toml
# Change baseurl before deploy
baseurl = "https://starter.stack.cai.im/"
locale = "en-us"
title = "Hugo Theme Stack Starter"

# Theme i18n support
# See all available values at https://github.com/CaiJimmy/hugo-theme-stack/tree/master/i18n
defaultContentLanguage = "en"

# Set hasCJKLanguage to true if DefaultContentLanguage is in [zh-cn ja ko]
# This will make .Summary and .WordCount behave correctly for CJK languages.
hasCJKLanguage = false

# Change it to your Disqus shortname before using
disqusShortname = "hugo-theme-stack"

[pagination]
pagerSize = 5
```


**关键字段解读**：

| 字段 | 作用 | 你的任务 |
|------|------|---------|
| `baseurl` | 网站根地址 | **必须改成** `https://<你的用户名>.github.io/` |
| `title` | 浏览器标签栏显示的网站标题 | 改成你的博客名 |
| `defaultContentLanguage` | 默认语言 | 中文博客可改为 `zh-cn` |
| `hasCJKLanguage` | 是否中日韩语言 | 中文改为 `true`（影响字数统计） |
| `pagerSize` | 每页文章数 | 可按需调整 |

### 2.2 `params.toml` —— Stack 主题的“控制面板”

这是 Stack 主题最核心的配置文件，控制着博客的视觉和行为：

```bash
cat config/_default/params.toml
```

```toml
# Pages placed under these sections will be shown on homepage and archive page.
mainSections = ["post"]

# Output page's full content in RSS.
rssFullContent = true

favicon = "img/favicon.png"

# Accepted values: "default", "lastmod"
# default = see https://gohugo.io/quick-reference/glossary/#default-sort-order
# lastmod = sort by last modified date, in descending order
SortBy = "default"

[footer]
since = 2020
customText = ""

[dateFormat]
published = ":date_full"
lastUpdated = ":date_full"

[sidebar]
emoji = ""
subtitle = "Lorem ipsum dolor sit amet, consectetur adipiscing elit."
avatar = "img/avatar.png"

[article]
headingAnchor = false
math = false
readingTime = true

[article.license]
enabled = true
default = "Licensed under CC BY-NC-SA 4.0"

[widgets]
homepage = [
    { type = "search" },
    { type = "archives", params = { limit = 5 } },
    { type = "categories", params = { limit = 10 } },
    { type = "tag-cloud", params = { limit = 10 } },
]
page = [{ type = "toc" }]

[opengraph.twitter]
site = ""
card = "summary_large_image"

[colorScheme]
toggle = true
default = "auto"

## Comments
[comments]
enabled = true
provider = "disqus"
```

**重点关注**：

| 配置块 | 作用 | 实验任务 |
|--------|------|---------|
| `[sidebar]` | 侧边栏的标语、头像、表情符号 | 修改 `subtitle` 为你自己的简介 |
| `[footer]` | 页脚的起始年份 | 将 `since` 改为当前年份 |
| `[widgets]` | 主页侧边栏显示哪些小工具 | 尝试调整顺序或增减组件 |
| `[colorScheme]` | 深色/浅色模式 | `default = "dark"` 强制深色模式 |
| `[comments]` | 评论系统开关 | 暂时 `enabled = false` 关闭评论 |

### 2.3 `menu.toml` —— 导航菜单

控制顶部导航栏和社交媒体链接：

```bash
cat config/_default/menu.toml
```

```toml
# Configure main menu and social menu
#[[main]]
#identifier = "home"
#name = "Home"
#url = "/"
#[main.params]
#icon = "home"
#newtab = true

[[social]]
identifier = "github"
name = "GitHub"
url = "https://github.com/CaiJimmy/hugo-theme-stack"
[social.params]
icon = "brand-github"

[[social]]
identifier = "twitter"
name = "Twitter"
url = "https://twitter.com"
[social.params]
icon = "brand-twitter"
```

**你的任务**：
- 把 GitHub 链接改成你自己的 GitHub 主页
- 取消注释 `[[main]]` 部分，添加一个“首页”菜单项
- 添加你的其他社交链接（如 B站、知乎等）

### 2.4 `module.toml` —— 主题引用

这是 Hugo Modules 的配置文件，告诉 Hugo 从哪里加载 Stack 主题：

```bash
cat config/_default/module.toml
```

```toml
[[imports]]
path = "github.com/CaiJimmy/hugo-theme-stack/v4"
```

> **知识点**：`/v4` 表示使用主题的 v4 版本。这就是为什么之前我们说“主题会自动更新”——GitHub Actions 的 `update-theme.yml` 工作流会定期运行 `hugo mod get -u` 来获取主题的最新版本。

### 2.5 其他配置文件（简要了解）

| 文件 | 作用 |
|------|------|
| `_languages.toml` | 多语言支持，可配置中英文切换 |
| `markup.toml` | Markdown 渲染选项（代码高亮、图片处理等） |
| `permalinks.toml` | URL 结构定制，如 `/posts/2024/01/标题/` 格式 |
| `related.toml` | 相关文章推荐算法配置 |

---

## 第三部分：实验课 —— 通过“破坏”来学习（10 分钟）

### 🧪 实验 1：修改 baseurl —— 最经典的“踩坑”

**操作**：把 `config.toml` 中的 `baseurl` 改成一个错误的地址。

```bash
nano config/_default/config.toml
# 把 baseurl 改成 "https://wrong-url/"
```

保存后，提交并推送：

```bash
git add .
git commit -m "实验：故意写错 baseurl"
git push origin main
```

**观察**：
1. 去 GitHub 仓库的 **Actions** 选项卡，查看部署是否失败
2. 点击失败的工作流，查看错误日志

**修复**：把 `baseurl` 改回正确的 `https://<你的用户名>.github.io/`，再次推送。

> **学到的原理**：`baseurl` 是 Hugo 生成所有内部链接的基准。如果写错，生成的 HTML 中的 CSS、JS 路径都会指向错误地址，导致页面样式全乱。

### 🧪 实验 2：关闭评论系统

**操作**：在 `params.toml` 中找到评论配置：

```toml
[comments]
enabled = true
provider = "disqus"
```

把 `enabled = true` 改为 `enabled = false`。

**本地预览验证**：

```bash
hugo server
```

刷新 `http://localhost:1313`，打开任意一篇文章，评论框消失了。

**推送上线**：

```bash
git add .
git commit -m "关闭评论系统"
git push origin main
```

> **学到的原理**：Stack 主题通过 `params.toml` 中的 `[comments]` 配置块控制评论功能的开关。`provider` 字段支持 `disqus`、`giscus`、`utterances` 等多种评论系统。

### 🧪 实验 3：修改侧边栏 —— 让博客有个性

**操作**：编辑 `params.toml` 中的 `[sidebar]` 部分：

```toml
[sidebar]
emoji = "🚀"
subtitle = "一名热爱技术的全栈开发者 | 探索 DevOps 与云原生"
avatar = "img/avatar.png"
```

**本地预览**：运行 `hugo server`，刷新查看侧边栏变化。

**进一步挑战**：替换头像图片
1. 准备一张正方形图片（建议 200x200 像素）
2. 放到 `assets/img/` 目录下，命名为 `avatar.png`
3. 确保 `params.toml` 中的 `avatar = "img/avatar.png"` 指向正确

### 🧪 实验 4：调整侧边栏小工具

**操作**：在 `params.toml` 中找到 `[widgets]` 部分：

```toml
[widgets]
homepage = [
    { type = "search" },
    { type = "archives", params = { limit = 5 } },
    { type = "categories", params = { limit = 10 } },
    { type = "tag-cloud", params = { limit = 10 } },
]
```

尝试：
1. 调整顺序（把 `search` 放到最后）
2. 修改 `limit` 数值（如把 `archives` 的 `limit` 改为 `10`）
3. 删除某个小工具（如删掉 `tag-cloud`）

**本地预览验证**，满意后推送。

---

## 第四部分：理解 GitHub Actions 工作流（5 分钟）

现在你已经对配置文件有了深入了解，让我们看看 GitHub Actions 是如何利用这些配置来构建和部署的。

### 4.1 `deploy.yml` —— 部署工作流

打开部署工作流：

```bash
cat .github/workflows/deploy.yml
```

关键部分解读：

```yaml
name: Build and deploy
on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:    # 允许手动触发

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      DART_SASS_VERSION: 1.97.1
      GO_VERSION: 1.25.5
      HUGO_VERSION: 0.154.2
      NODE_VERSION: 24.12.0
    steps:
      - name: Checkout
        uses: actions/checkout@v6
      - name: Setup Go
        uses: actions/setup-go@v6
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build the site
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/" \
            --cacheDir "${{ runner.temp }}/hugo_cache"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v5
        with:
          path: ./public
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

**关键知识点**：

| 部分 | 作用 |
|------|------|
| `on: push` | 每次推送到 `main` 或 `master` 分支自动触发 |
| `env:` | 定义了构建环境各工具的版本（Hugo、Go、Node.js、Dart Sass） |
| `actions/checkout@v6` | 拉取你的代码 |
| `peaceiris/actions-hugo@v3` | 安装 Hugo（`extended: true` 表示安装扩展版） |
| `hugo --minify` | 构建并压缩生成的 HTML/CSS/JS |
| `actions/upload-pages-artifact@v5` | 将 `./public` 目录打包为部署 artifact |
| `actions/deploy-pages@v5` | 将 artifact 部署到 GitHub Pages |

### 4.2 `update-theme.yml` —— 自动更新主题

这个工作流每天 UTC 0 点自动运行：

```yaml
name: Update theme
on:
  schedule:
    - cron: "0 0 * * *"    # 每天 UTC 0 点
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

> **学到的原理**：`hugo mod get -u` 会拉取主题的最新版本并更新 `go.mod` 和 `go.sum`。`git-auto-commit-action` 会自动提交这些变更，然后触发 `deploy.yml` 重新构建部署。整个过程全自动！

---

## 第五部分：你的家庭作业

1. **定制博客名称和标语**：修改 `config.toml` 的 `title` 和 `params.toml` 的 `subtitle`

2. **添加“关于”页面**：
   - 在 `content/page/` 目录下创建 `about/index.md`
   - 写入你的个人介绍
   - 在 `menu.toml` 中添加一个指向 `/about/` 的菜单项

3. **强制深色模式**：将 `params.toml` 中的 `[colorScheme]` 的 `default` 改为 `"dark"`

4. **（挑战）添加一个新社交链接**：在 `menu.toml` 的 `[[social]]` 部分添加你的 B站或知乎链接

5. **（挑战）理解错误**：故意把 `config.toml` 中的 `hasCJKLanguage` 设为 `true`，写一篇包含中文和英文混排的文章，观察字数统计的变化

---

## 本课总结

| 概念 | 你做了什么 | 你学到了什么 |
|------|-----------|-------------|
| **配置文件层次** | 浏览了 `config/_default/` 下的所有文件 | Hugo 通过多个 TOML 文件组织配置，各司其职 |
| **params.toml** | 修改了侧边栏、小工具、评论开关 | Stack 主题的所有可定制项都集中在这里 |
| **menu.toml** | 修改了社交链接 | 导航菜单和社交链接的配置范式 |
| **“破坏-修复”循环** | 故意改错 baseurl，观察构建失败 | 理解配置项之间的依赖关系和错误排查方法 |
| **GitHub Actions** | 阅读了 `deploy.yml` 和 `update-theme.yml` | 理解自动化部署和主题自动更新的实现机制 |

---

*“实验是检验真理的唯一标准。先破坏，再修复，最后理解 —— 这才是工程师的学习方式。”*
