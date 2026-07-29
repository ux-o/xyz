---
title: 10分钟上线个人博客
---

# 第一课：10分钟上线个人博客

## 🎯 课程目标

**在10分钟内，从零开始在 Arch Linux 上搭建一个可公开访问的个人技术博客。**

这不是“学习原理”，而是 **“看到成品”** 。你不需要理解背后的复杂机制，只需要亲手完成一次完整的“写作→推送→上线”流程，亲眼看到自己的网站出现在互联网上。

**最终成果**：`https://<你的GitHub用户名>.github.io` 可访问的博客站点。

---

## ⏱️ 课程总览

| 步骤 | 内容 | 预计时间 |
|------|------|----------|
| 1 | 安装必要工具（Git、Go、Hugo） | 2分钟 |
| 2 | 配置 GitHub SSH 密钥 | 2分钟 |
| 3 | Fork 模板仓库并克隆到本地 | 1分钟 |
| 4 | 修改配置并本地预览 | 2分钟 |
| 5 | 推送到 GitHub，触发自动部署 | 2分钟 |
| 6 | 访问你的博客！ | 1分钟 |

---

## 步骤 1：安装必要工具

> **为何需要这些工具？**
> - **Git**：版本控制，用来和 GitHub 交互。
> - **Go**：Hugo 使用 Go Modules 管理主题依赖。
> - **Hugo (Extended 版)**：静态网站生成器，Extended 版本支持 Sass/SCSS。

在终端中依次执行以下命令：

```bash
# 1. 安装 Git（通常 Arch Linux 已预装）
sudo pacman -S git

# 2. 安装 Go（Hugo Modules 需要 Go 1.18 或更高版本）
sudo pacman -S go

# 3. 安装 Hugo Extended 版
# Arch Linux 官方仓库中 hugo 包就是 extended 版本
sudo pacman -S hugo

# 4. 验证安装
git --version
go version
hugo version
```

如果 `hugo version` 输出中包含 `+extended` 字样，说明安装成功。

---

## 步骤 2：配置 GitHub SSH 密钥

> **为何需要 SSH 密钥？**
> 让你无需每次输入密码就能安全地推送代码到 GitHub。

### 2.1 生成 SSH 密钥对

```bash
# 生成 Ed25519 密钥（更安全、更高效）
ssh-keygen -t ed25519 -C "你的邮箱@example.com"

# 一路按回车使用默认设置即可
```

### 2.2 将公钥添加到 GitHub

```bash
# 显示公钥内容，复制整段输出
cat ~/.ssh/id_ed25519.pub
```

1. 打开浏览器，登录 [GitHub](https://github.com)
2. 点击右上角头像 → **Settings**
3. 左侧菜单选择 **SSH and GPG keys**
4. 点击 **New SSH key**
5. **Title** 填写 `Arch Linux`（或任何你记得的名字）
6. **Key** 粘贴刚才复制的公钥内容
7. 点击 **Add SSH key**

### 2.3 测试 SSH 连接

```bash
ssh -T git@github.com
```

如果看到 `Hi <你的用户名>! You've successfully authenticated...`，说明配置成功。

---

## 步骤 3：Fork 模板仓库并克隆到本地

> **为何使用模板？**
> 我们不从零搭建，而是直接使用一个**已经配置好主题和 GitHub Actions** 的模板。这样你不需要写任何配置文件，10分钟内就能看到成果。

### 3.1 Fork 模板

1. 打开模板仓库：[https://github.com/CaiJimmy/hugo-theme-stack-starter](https://github.com/CaiJimmy/hugo-theme-stack-starter)
2. 点击右上角绿色的 **“Use this template”** 按钮
3. 选择 **“Create a new repository”**
4. **Repository name** 必须填写：**`<你的GitHub用户名>.github.io`**（例如我的用户名是 `zhangsan`，就填 `zhangsan.github.io`）
5. 确保仓库设置为 **Public**（公开）
6. 点击 **Create repository from template**

### 3.2 克隆到本地

```bash
# 克隆你刚刚创建的仓库（注意替换用户名）
git clone git@github.com:<你的用户名>/<你的用户名>.github.io.git

# 进入项目目录
cd <你的用户名>.github.io
```

---

## 步骤 4：修改配置并本地预览

> **为何要修改配置？**
> 模板里默认的网站地址是 `example.com`，你需要把它改成你自己的 GitHub Pages 地址。

### 4.1 修改站点地址

用你喜欢的编辑器（如 VSCode、Neovim 或 nano）打开配置文件：

```bash
# 使用 nano 编辑器（Arch Linux 默认已安装）
nano config/_default/config.toml
```

找到这一行：
```toml
baseurl = "https://example.com/"
```

改成：
```toml
baseurl = "https://<你的用户名>.github.io/"
```

保存并退出（nano 中按 `Ctrl+X`，然后 `Y`，再 `Enter`）。

### 4.2 本地预览（见证奇迹！）

```bash
# 启动本地开发服务器
hugo server
```

你会看到类似这样的输出：
```
Web Server is available at http://localhost:1313/
```

打开浏览器，访问 `http://localhost:1313/`，你就能看到你的博客在本地跑起来了！

> 💡 **提示**：修改任何文件后，浏览器会自动刷新预览。你可以随意逛逛，看看这个博客长什么样。

按 `Ctrl+C` 可以停止服务器。

---

## 步骤 5：推送到 GitHub，触发自动部署

> **为何推送就能自动部署？**
> 模板里已经配置好了 **GitHub Actions** 工作流。当你推送代码到 `main` 分支时，GitHub 会自动执行构建并部署到 GitHub Pages。

```bash
# 添加所有修改
git add .

# 提交修改（-m 后面是提交信息，可以随意写）
git commit -m "修改 baseurl 为我的 GitHub Pages 地址"

# 推送到 GitHub
git push origin main
```

### 5.1 观察自动部署过程

1. 打开你的 GitHub 仓库页面：`https://github.com/<你的用户名>/<你的用户名>.github.io`
2. 点击顶部的 **Actions** 选项卡
3. 你会看到一个名为 “Deploy Hugo site to Pages” 的工作流正在运行（黄色圆点表示运行中）
4. 等待约 1-2 分钟，当圆点变成绿色的 ✓，表示部署成功

### 5.2 配置 GitHub Pages（如果第一次部署失败）

如果 Actions 运行失败，可能需要进行以下配置：

1. 在你的 GitHub 仓库页面，点击 **Settings**
2. 左侧菜单选择 **Actions** → **General**
3. 在 **Workflow permissions** 部分，选择 **“Read and write permissions”**
4. 点击 **Save**
5. 左侧菜单选择 **Pages**
6. 在 **Source** 处选择 **“GitHub Actions”**
7. 重新推送一次代码：`git push origin main`

---

## 步骤 6：访问你的博客！

在浏览器中打开：

```
https://<你的GitHub用户名>.github.io
```

**恭喜！你的个人技术博客已经上线了！** 🎉

---

## 📝 课后探索任务

在你看到成品、产生好奇心之后，可以尝试以下探索：

### 探索 1：修改网站标题
- 打开 `config/_default/config.toml`
- 找到 `title = "..."` 这一行，改成你想要的名字
- 保存，推送，观察变化

### 探索 2：写一篇自己的博客
```bash
# 创建一篇新文章
hugo new posts/我的第一篇博客/index.md
```
- 用编辑器打开 `content/posts/我的第一篇博客/index.md`
- 将 `draft: true` 改为 `draft: false`
- 在 `---` 下方用 Markdown 写内容
- 推送，上线！

### 探索 3：看看背后发生了什么
- 打开 `.github/workflows/` 目录下的 `.yml` 文件
- 找找看：`hugo` 命令在哪里被执行？部署到 Pages 的步骤是什么？
- （这就是下一课的内容）

---

## 🔧 常见问题

**Q: `hugo: command not found`**
A: 运行 `sudo pacman -S hugo` 重新安装。

**Q: `Permission denied (publickey)` 推送失败**
A: 重新执行步骤 2，确保公钥已添加到 GitHub。

**Q: 部署后网站是 404**
A: 检查 `config/_default/config.toml` 中的 `baseurl` 是否填写正确，必须包含 `https://` 和你的完整用户名。

**Q: 推送后 Actions 一直显示黄色**
A: 等待几分钟，第一次部署可能需要稍长时间。如果超过 5 分钟，点击进入查看日志找错误原因。

---

## 💎 本课总结

你刚刚完成了一次完整的 **“写作 → 推送 → 自动部署 → 上线”** 流程。在这个过程中：

- 你**没有**写任何 HTML/CSS/JavaScript
- 你**没有**手动配置服务器
- 你**没有**学习复杂的 Git 命令

你只是：
1. 安装了 3 个工具
2. 配置了 1 个 SSH 密钥
3. Fork 了 1 个模板
4. 修改了 1 行配置
5. 推送了 1 次代码

**10 分钟，一个属于你自己的网站就上线了。**

这就是现代工具链的力量。下一课，我们将开始 **“破坏与探索”** ——当你想要修改侧边栏、改变颜色、添加导航菜单时，会发生什么？
