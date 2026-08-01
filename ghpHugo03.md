# 第三课：内容创作 —— 用 Markdown 写出你的第一篇博客

**教学理念**：延续“自上而下”的探究式学习。前两课你学会了*搭建*和*配置*博客，这一课我们来学习*创作*——这才是博客的灵魂所在。Hugo 的核心设计哲学是“内容即文件”：你用 Markdown 写作，Hugo 负责把它变成漂亮的网页。我们将从“写一篇文章”开始，通过实验逐步掌握 Hugo 内容管理的全部核心概念。

---

## 本课目标

完成本课后，你将能够：
- 理解 Hugo 的内容组织结构（Page Bundle、Section）
- 熟练使用 Front Matter 控制文章的元数据与显示
- 在文章中插入图片、视频等多媒体资源
- 使用 Shortcodes 实现 Markdown 无法完成的复杂排版
- 用分类（Categories）和标签（Tags）组织你的文章体系

---

## 第一部分：内容组织结构 —— 你的文章放在哪里？（3 分钟）

首先看看你博客的 `content/` 目录：

```bash
tree content/
```

你应该看到类似这样的结构：

```
content/
├── _index.md           # 首页内容
├── categories/         # 分类归档页面（自动生成）
│   └── _index.md
├── page/               # 独立页面（关于、友链等）
│   ├── about/
│   │   └── index.md
│   └── links/
│       └── index.md
└── post/               # ⭐ 所有博客文章
    ├── _index.md
    ├── chinese-test/
    │   └── index.md
    ├── emoji-support/
    │   └── index.md
    ├── markdown-syntax/
    │   └── index.md
    ├── math-typesetting/
    │   └── index.md
    ├── placeholder-text/
    │   └── index.md
    ├── rich-content/
    │   └── index.md
    └── typography/
        └── index.md
```

### 1.1 两种内容组织方式

Hugo 支持两种方式来组织一篇文章：

| 方式 | 结构 | 适用场景 |
|------|------|---------|
| **单文件模式** | `posts/hello.md` | 简单文章，无图片等资源 |
| **Page Bundle（推荐）** | `posts/hello/index.md` + `posts/hello/img/` | 包含图片、附件的复杂文章 |

**为什么推荐 Page Bundle？** 把图片和文章放在同一个文件夹里，迁移、备份、维护都更方便。你的模板默认就是这种结构。

### 1.2 Section（栏目）的概念

`content/` 下的**一级目录**就是 Hugo 的 **Section**：

- `post/` —— 博客文章栏目（`mainSections` 配置指定了这里）
- `page/` —— 独立页面栏目（关于、友链等）
- `categories/` —— 分类归档（自动生成，一般不需要手动添加文章）

> **实验**：查看 `config/_default/params.toml` 中的 `mainSections = ["post"]`，它告诉 Hugo 哪些栏目是“主要文章”，会在首页和归档页显示。

---

## 第二部分：创建你的第一篇文章（5 分钟）

### 2.1 使用 Hugo 命令创建文章

```bash
hugo new content posts/my-first-blog/index.md
```

打开生成的文件：

```bash
cat content/posts/my-first-blog/index.md
```

你会看到：

```yaml
---
title: "My First Blog"
date: 2026-07-31T14:30:00+08:00
draft: true
---
```

这就是 **Front Matter** —— 文章的元数据。

### 2.2 Front Matter 详解

Front Matter 支持 YAML（`---`）、TOML（`+++`）、JSON 三种格式。模板默认使用 **YAML**。

**常用字段一览**：

| 字段 | 作用 | 示例 |
|------|------|------|
| `title` | 文章标题 | `"我的第一篇博客"` |
| `date` | 发布日期 | `2026-07-31T14:30:00+08:00` |
| `draft` | 是否为草稿（`true` 不发布） | `false` |
| `lastmod` | 最后修改时间 | `2026-08-01T10:00:00+08:00` |
| `description` | 文章摘要（SEO 和列表页显示） | `"Hugo 博客搭建全记录"` |
| `categories` | 分类（数组） | `["Hugo", "教程"]` |
| `tags` | 标签（数组） | `["静态网站", "Markdown"]` |
| `slug` | 自定义 URL 最后一段 | `"hugo-guide"` |
| `aliases` | 旧链接跳转（301 重定向） | `["/old-post/"]` |
| `weight` | 排序权重（越小越靠前） | `10` |
| `featured_image` | 封面图 | `"img/cover.jpg"` |

### 2.3 🧪 实验 1：修改 Front Matter，观察变化

**操作**：编辑 `content/posts/my-first-blog/index.md`：

```yaml
---
title: "我的第一篇博客 —— Hugo 从零搭建"
date: 2026-07-31T14:30:00+08:00
draft: false                    # ← 改为 false，准备发布
description: "记录我用 Hugo + Stack 主题搭建个人博客的完整过程"
categories: ["Hugo", "教程"]
tags: ["静态网站", "Markdown", "GitHub Pages"]
---
```

**本地预览**：

```bash
hugo server
```

访问 `http://localhost:1313`，你的新文章已经出现在首页列表里了。点击进去，标题、日期、分类、标签都正确显示。

> **关键洞察**：`draft: false` 是文章能否被看到的关键。`hugo server` 默认会显示草稿，但 `hugo` 构建生产版本时**不会**包含草稿。

---

## 第三部分：文章内容写作 —— Markdown 进阶（5 分钟）

### 3.1 基础 Markdown

在 Front Matter 下方，用 Markdown 写正文：

```markdown
## 为什么选择 Hugo？

Hugo 是目前**最快**的静态网站生成器。它的核心优势是：

1. **速度极快**：构建一个包含数百篇文章的网站仅需几毫秒
2. **无依赖**：生成的是纯静态 HTML，无需数据库
3. **灵活**：支持多种内容组织和主题系统

### 安装 Hugo

在 Arch Linux 上安装 Hugo 非常简单：

```bash
sudo pacman -S hugo
```

### 参考资源

- [Hugo 官方文档](https://gohugo.io/)
- [Stack 主题文档](https://github.com/CaiJimmy/hugo-theme-stack)
```

### 3.2 插入图片

**推荐做法**：在文章文件夹下创建 `img/` 目录存放图片。

```bash
# 创建图片目录
mkdir -p content/posts/my-first-blog/img/

# 将你的图片（如 cover.jpg）复制进去
cp ~/Pictures/cover.jpg content/posts/my-first-blog/img/
```

在 Markdown 中引用图片：

```markdown
![博客封面](img/cover.jpg "Hugo 博客搭建教程封面")
```

路径说明：`img/cover.jpg` 是相对于 `index.md` 所在目录的路径。

### 3.3 🧪 实验 2：插入图片并预览

1. 找一张图片放到 `content/posts/my-first-blog/img/` 下
2. 在文章正文中插入 `![描述](img/文件名.jpg)`
3. 运行 `hugo server` 预览效果

---

## 第四部分：Shortcodes —— 超越 Markdown 的魔法（5 分钟）

Markdown 能做的事情有限。当你需要插入视频、高亮代码、提示框等复杂元素时，就需要 **Shortcodes**。

Shortcode 本质上是一个**模板**，在 Markdown 中被调用。Hugo 内置了常用的 Shortcodes，Stack 主题也扩展了一些。

### 4.1 常用内置 Shortcodes

| Shortcode | 作用 | 示例 |
|-----------|------|------|
| `youtube` | 嵌入 YouTube 视频 | `{{</* youtube 视频ID */>}}` |
| `vimeo` | 嵌入 Vimeo 视频 | `{{</* vimeo 视频ID */>}}` |
| `highlight` | 语法高亮代码块 | `{{</* highlight go */>}}...{{</* /highlight */>}}` |
| `figure` | 带标题的图片 | `{{</* figure src="img/cover.jpg" title="封面图" */>}}` |
| `details` | 可折叠详情框 | `{{</* details "点击展开" */>}}内容{{</* /details */>}}` |
| `param` | 引用 Front Matter 参数 | `{{</* param "description" */>}}` |

### 4.2 🧪 实验 3：嵌入 YouTube 视频

在你的文章末尾添加：

```markdown
## 视频教程

{{< youtube dQw4w9WgXcQ >}}
```

> **注意**：`{{<` 和 `>`} 是 Shortcode 的调用语法。`youtube` 后面跟的是视频 ID（从视频 URL 中提取）。

### 4.3 Stack 主题的自定义 Shortcodes

Stack 主题还提供了额外的 Shortcodes（查看 `layouts/shortcodes/` 目录）：

- **`< bilibili >`**：嵌入 B 站视频（如果主题支持）
- **`< notice >`**：提示框（注意、警告、提示等）

---

## 第五部分：分类与标签 —— 组织你的文章体系（3 分钟）

### 5.1 Taxonomies（分类法）

Hugo 使用 **Taxonomies** 来组织内容。默认有两个：

- **Categories（分类）**：粗粒度的主题划分，如“Hugo”、“DevOps”、“Linux”
- **Tags（标签）**：细粒度的关键词，如“静态网站”、“自动化部署”

在 Front Matter 中这样设置：

```yaml
categories: ["Hugo", "DevOps"]
tags: ["静态网站", "GitHub Actions", "Markdown"]
```

### 5.2 🧪 实验 4：创建分类和标签页面

Hugo 会自动为每个 category 和 tag 生成归档页面。访问：

- `http://localhost:1313/categories/hugo/` —— 所有“Hugo”分类的文章
- `http://localhost:1313/tags/静态网站/` —— 所有“静态网站”标签的文章

### 5.3 自定义分类法

你还可以在 `config/_default/config.toml` 中定义自己的分类法：

```toml
[taxonomies]
category = "categories"
tag = "tags"
series = "series"    # 新增“系列”分类
```

然后在 Front Matter 中使用：

```yaml
series: ["Hugo 从零搭建"]
```

---

## 第六部分：实验课 —— 通过“破坏”来学习（5 分钟）

### 🧪 实验 5：草稿 vs 发布

**操作**：把文章的 `draft` 改为 `true`：

```yaml
draft: true
```

**本地预览**：`hugo server` 依然能看到文章（默认显示草稿）。

**生产构建测试**：

```bash
hugo                    # 构建生产版本
ls public/posts/        # 查看生成的 HTML
```

你会发现 `my-first-blog/` 目录**没有**被生成。因为生产构建默认**排除草稿**。

**修复**：把 `draft` 改回 `false`，重新构建。

### 🧪 实验 6：错误的日期格式

**操作**：把 `date` 改成错误格式：

```yaml
date: "2026-07-31"      # 缺少时间部分
```

**本地预览**：Hugo 可能无法正确解析，导致文章排序异常或根本不显示。

**修复**：改回正确格式 `2026-07-31T14:30:00+08:00`。

### 🧪 实验 7：缺失必填字段

**操作**：删除 `title` 字段。

**本地预览**：文章标题变成了文件名（`my-first-blog`）或空白。

**修复**：恢复 `title` 字段。

---

## 第七部分：完整示例 —— 一篇“完美”的文章

下面是 Stack 主题下一篇完整文章的 Front Matter 示例：

```yaml
---
title: "Hugo + GitHub Pages 搭建个人博客完整指南"
date: 2026-07-31T14:30:00+08:00
lastmod: 2026-08-01T10:00:00+08:00
draft: false
description: "从零开始，用 Hugo 和 GitHub Pages 搭建一个属于你自己的技术博客"
categories: ["Hugo", "DevOps"]
tags: ["静态网站", "GitHub Actions", "Markdown", "自动化部署"]
series: ["博客搭建系列"]
featured_image: "img/cover.jpg"
weight: 1
aliases: ["/hugo-guide/"]
---
```

对应的文章正文示例：

```markdown
## 前言

作为一名开发者，拥有一个属于自己的技术博客是非常重要的。

## 为什么选择 Hugo？

Hugo 是**最快**的静态网站生成器...

## 搭建步骤

### 1. 安装 Hugo

```bash
sudo pacman -S hugo
```

### 2. 创建站点

```bash
hugo new site myblog
```

## 视频教程

{{< youtube dQw4w9WgXcQ >}}

## 参考资源

- [Hugo 官方文档](https://gohugo.io/)
- [Stack 主题](https://github.com/CaiJimmy/hugo-theme-stack)

---

> **提示**：本文是系列第一篇，后续文章请关注分类 `Hugo` 下的更新。
```

---

## 本课总结

| 概念 | 你做了什么 | 你学到了什么 |
|------|-----------|-------------|
| **Page Bundle** | 在 `posts/文章名/` 下创建 `index.md` | 将文章和图片放在同一文件夹便于管理 |
| **Front Matter** | 修改了 `title`、`date`、`draft`、`categories`、`tags` | Front Matter 控制文章的所有元数据 |
| **图片插入** | 在文章目录下创建 `img/` 并引用图片 | 图片作为 Page Resources 与文章关联 |
| **Shortcodes** | 使用 `youtube` 嵌入视频 | Shortcode 是 Markdown 的“超能力” |
| **分类与标签** | 设置了 `categories` 和 `tags` | Taxonomies 是 Hugo 的内容组织方式 |
| **草稿机制** | 切换 `draft: true/false` | 草稿不会出现在生产构建中 |

---

## 你的家庭作业

1. **写一篇文章**：用 Markdown 写一篇不少于 300 字的技术文章，包含标题、正文、代码块、图片

2. **设置分类和标签**：为文章设置合适的 `categories` 和 `tags`

3. **嵌入一个视频**：使用 `youtube` 或 `vimeo` Shortcode 嵌入一个相关视频

4. **（挑战）创建“关于”页面**：在 `content/page/about/index.md` 中写你的个人介绍页面

5. **（挑战）创建文章系列**：在 `config.toml` 中添加 `series` 分类法，并在 Front Matter 中设置 `series: ["系列名"]`

---

*“写作是思考的载体。一个技术博客，不仅是知识的输出，更是思维的锤炼。”*
