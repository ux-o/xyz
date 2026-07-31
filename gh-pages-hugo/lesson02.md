# Lesson 2: Explore and Customize — The Skeleton of Your Blog

**Teaching Philosophy**: Now that you have a *live website*, it's time to understand *what makes it tick*. In Lesson 1, you saw the magic. In Lesson 2, you will open the box and look inside. You will break things, fix them, and make the blog truly your own.

This is the "top-down" approach in action: **Result → Experiment → Discover → Understand**.

---

## What You Will Achieve

By the end of this lesson, you will:
- Understand every folder in your Hugo project
- Customize your site's appearance and sidebar
- Add a new "About" page
- Create a template (archetype) for new posts
- Make your blog look and feel like *yours*, not a template

---

## Part 1: The Grand Tour — What's in Your Project? (5 minutes)

Your blog project is more than just Markdown files. Let's explore the directory structure.

### 1.1 Open Your Project

```bash
cd ~/<your-username>.github.io
ls -la
```

You should see something like this:

```
.
├── archetypes/
├── assets/
├── config/
├── content/
├── data/
├── i18n/
├── layouts/
├── static/
├── .git/
├── .github/
└── go.mod
```

### 1.2 What Each Folder Does

| Folder | Purpose | What You'll Put Here |
|--------|---------|---------------------|
| **`archetypes/`** | Templates for new content | Default front matter for new posts |
| **`assets/`** | Files processed by Hugo's asset pipeline | SCSS, JavaScript, images that need processing |
| **`config/`** | Site configuration | Your site's settings (title, theme, menus) |
| **`content/`** | Your actual content | Markdown files — your blog posts and pages |
| **`data/`** | Data files | JSON/TOML/YAML data for your templates |
| **`i18n/`** | Translation tables | Multi-language support |
| **`layouts/`** | HTML templates | How your content is displayed |
| **`static/`** | Files copied as-is | Images, favicon, robots.txt |
| **`.github/`** | GitHub Actions workflows | The automation that deploys your site |

### 1.3 The Most Important Folder: `content/`

This is where your blog posts live:

```bash
tree content/
```

You'll see something like:

```
content/
├── _index.md          # Homepage content
└── posts/             # All your blog posts
    ├── _index.md      # Blog listing page
    ├── my-first-post/
    │   └── index.md   # A post in a bundle
    └── another-post.md # A standalone post
```

> **Key Insight**: The folder structure inside `content/` **determines your site's URL structure**. A file at `content/posts/my-post.md` becomes `https://your-site.github.io/posts/my-post/`.

---

## Part 2: Customize Your Site's Look (10 minutes)

Now let's make this blog *yours* by changing how it looks.

### 2.1 Find the Configuration Files

All configuration lives in `config/_default/`:

```bash
ls config/_default/
```

You'll see files like:
- `config.toml` — Main site settings
- `params.toml` — Theme-specific parameters
- `menu.toml` — Navigation menu
- `languages.toml` — Language settings

### 2.2 Change the Site Title and Description

Open the main config:

```bash
nano config/_default/config.toml
```

Find these lines and change them:

```toml
title = "My Awesome Tech Blog"
```

Now find the `params` section. Add or modify:

```toml
[params]
    description = "A blog about software development, DevOps, and learning"
```

Save and exit.

### 2.3 Customize the Sidebar Widgets

The Stack theme has a right sidebar with widgets. Let's customize them.

Open `params.toml`:

```bash
nano config/_default/params.toml
```

Look for the `[widgets]` section. You'll see something like:

```toml
[widgets]
    homepage = ["search", "recent", "categories", "tag-cloud"]
    page = ["search", "toc", "recent"]
```

Try removing "recent" from the homepage widgets:

```toml
[widgets]
    homepage = ["search", "categories", "tag-cloud"]
    page = ["search", "toc", "recent"]
```

Save, then run `hugo server` and see the difference.

### 2.4 Add Your Social Links

Still in `params.toml`, find the `[social]` section:

```toml
[social]
    github = "https://github.com/<your-username>"
    twitter = "https://twitter.com/<your-handle>"
    # linkedin = "https://linkedin.com/in/<your-profile>"
```

Uncomment and add your own social links. These will appear in the sidebar.

---

## Part 3: Add a New Page — "About Me" (5 minutes)

Every blog needs an About page. Let's create one.

### 3.1 Create the About Page

In Hugo, pages go in the `content/` directory:

```bash
hugo new about/index.md
```

This creates `content/about/index.md` with some default front matter.

### 3.2 Edit the About Page

```bash
nano content/about/index.md
```

Change it to something like:

```yaml
---
title: "About Me"
date: 2026-07-27T10:00:00+08:00
draft: false
---

## Who I Am

I'm a computer science student at [Your University]. I'm passionate about:

- Software development
- DevOps and automation
- Open source

## Why This Blog

I created this blog to document my learning journey and share knowledge with others.

## Contact

- GitHub: [@<your-username>](https://github.com/<your-username>)
- Email: your.email@example.com
```

### 3.3 Add the About Page to the Menu

Open the menu configuration:

```bash
nano config/_default/menu.toml
```

Add a new entry:

```toml
[[main]]
    name = "About"
    pageRef = "/about/"
    weight = 10
```

The `weight` determines the order in the menu (lower numbers appear first).

### 3.4 Preview and Deploy

```bash
hugo server
```

Visit `http://localhost:1313/about/` to see your new page.

When ready:

```bash
git add .
git commit -m "Added About page and customized sidebar"
git push origin main
```

Watch GitHub Actions deploy your changes.

---

## Part 4: Create a Post Template (Archetype) (5 minutes)

Every time you write a new post, you manually type the same front matter. **Archetypes** automate this.

### 4.1 Create a Custom Archetype for Posts

```bash
mkdir -p archetypes
nano archetypes/posts.md
```

Add this content:

```yaml
---
title: "{{ replace .File.ContentBaseName `-` ` ` | title }}"
date: {{ .Date }}
draft: true
tags: []
categories: []
description: ""
---

## Introduction

<!-- Write your introduction here -->

## Main Content

<!-- Your main content goes here -->

## Conclusion

<!-- Summarize your post -->
```

### 4.2 Create a New Post Using the Archetype

Now when you create a new post, Hugo will use your template:

```bash
hugo new posts/my-awesome-post/index.md
```

Open the file and see the pre-filled front matter and structure!

### 4.3 Write Your First Real Blog Post

Fill in the content. Write about something you learned recently. Don't worry about perfection — just write.

---

## Part 5: Experiment — Break Things on Purpose (5 minutes)

The best way to learn is to break things and fix them.

### 5.1 Experiment 1: Change the Theme Color

Open `config/_default/params.toml` and find:

```toml
[color]
    scheme = "auto"  # or "light", "dark"
```

Change it to `"dark"` and see what happens. Then try `"light"`.

### 5.2 Experiment 2: Add a Custom Widget

Create a custom sidebar widget:

```bash
mkdir -p layouts/partials/widget
nano layouts/partials/widget/custom.html
```

Add this content:

```html
<div class="widget">
    <h3>📚 Quote of the Day</h3>
    <p>"The best way to predict the future is to create it."</p>
</div>
```

Now add it to `params.toml`:

```toml
[widgets]
    homepage = ["search", "custom", "categories", "tag-cloud"]
```

Restart `hugo server` and see your custom widget appear!

### 5.3 Experiment 3: Deliberately Break the Site

Change `baseurl` in `config.toml` to something wrong:

```toml
baseurl = "https://wrong-url/"
```

Push this change. Watch GitHub Actions fail. Fix it, push again, and watch it succeed.

> **This is the most important lesson**: You now know how to **debug** your own site. The error messages in GitHub Actions are your friends — read them carefully.

---

## Part 6: Understanding the Automation (5 minutes)

Now that you've made changes, let's understand *how* they get deployed.

### 6.1 The Workflow File

Open the GitHub Actions workflow:

```bash
cat .github/workflows/gh-pages.yml
```

You'll see something like:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: 'stable'
      - uses: actions/setup-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true
      - run: hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
```

### 6.2 What Each Section Does

| Section | Purpose |
|---------|---------|
| `on: push` | Triggers on every push to `main` |
| `runs-on: ubuntu-latest` | Runs on a fresh Ubuntu machine |
| `actions/checkout` | Downloads your code |
| `actions/setup-hugo` | Installs Hugo with Go modules support |
| `hugo --minify` | Builds your site (minifies HTML/CSS/JS) |
| `upload-pages-artifact` | Saves the built site |
| `deploy-pages` | Deploys to GitHub Pages |

### 6.3 The cron job for automatic theme updates

Some starter templates include a cron job to automatically update the theme daily. Check if yours has it:

```bash
cat .github/workflows/update-theme.yml
```

If it exists, it will run on a schedule to keep your theme current.

---

## Summary: What You Learned

| Concept | What You Did | Why It Matters |
|---------|--------------|----------------|
| **Directory Structure** | Explored every folder | You know where everything goes |
| **Configuration** | Changed title, description, widgets | You control the look and feel |
| **Content Organization** | Created an About page | You understand URLs and sections |
| **Archetypes** | Created a post template | You save time writing new posts |
| **Customization** | Added a custom widget | You can extend the theme |
| **Debugging** | Broke and fixed the site | You can troubleshoot problems |
| **Workflow** | Read the GitHub Actions file | You understand the automation |

---

## Your Homework (For Next Class)

1. **Write a real post**: Create a new post using your archetype. Write about something you learned in Lesson 1 or 2. Include a code block, a list, and an image.

2. **Customize the footer**: Find where the footer text comes from (hint: look in `config/_default/params.toml` for `copyright`). Change it to your name.

3. **Add a new section**: Create a `content/notes/` directory. Create an `_index.md` file inside it. Add a few notes. How does the URL structure work? What happens if you add it to the menu?

4. **Read the docs**: Skim the Hugo documentation on [Content Organization](https://gohugo.io/content-management/organization/). Understanding this will make everything else easier.

---

## Key Insight

**You now own this blog.** It's not a template anymore — it's *your* site. You know where the settings are, how to add pages, and how to customize the look.

The "top-down" approach has taken you from:

**Lesson 1**: "I have a website!" → **Lesson 2**: "I understand how it works and can change anything I want."

Next: We'll dive deeper into Hugo's template system and learn how to create custom layouts for different types of content.

---

*"The only way to learn a new programming language is by writing programs in it." — Dennis Ritchie*
