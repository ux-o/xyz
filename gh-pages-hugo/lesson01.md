---
title: Live Blog in 10 Minutes
---

# Lesson 1: Your Blog Goes Live in 10 Minutes

**Teaching Philosophy**: Start with the *result* — a live website on the internet — then work backwards to understand *how* it happened. This is the "top-down" approach: wonder → experiment → discover → understand.

---

## What You Will Achieve

By the end of this lesson, you will have a live personal blog at `https://<your-username>.github.io`. You will see it with your own eyes in a browser. Everything else — the how, the why, the details — comes later.

---

## Part 1: Environment Setup (3 minutes)

Your machine is Arch Linux with GNOME Wayland. Root access is unrestricted. Let's get the tools.

### 1.1 Install Hugo

Hugo is a static site generator — it takes Markdown files and turns them into HTML pages.

```bash
sudo pacman -S hugo
```

Verify the installation:

```bash
hugo version
```

You should see output similar to `hugo v0.163.3+extended`.

### 1.2 Install Git

```bash
sudo pacman -S git
git --version
```

### 1.3 Install Go

The Stack theme uses Hugo Modules, which requires Go.

```bash
sudo pacman -S go
go version
```

---

## Part 2: Get the Template (2 minutes)

Instead of building from scratch, we will use a **starter template** — a complete, working blog that we can customize.

### 2.1 Fork the Starter Template

1. Open your browser and go to: **https://github.com/CaiJimmy/hugo-theme-stack-starter**
2. Click the green **"Use this template"** button
3. Select **"Create a new repository"**
4. Name your repository exactly: **`<your-username>.github.io`** (replace `<your-username>` with your actual GitHub username)
5. Make it **Public** (GitHub Pages requires this for free hosting)
6. Click **"Create repository"**

### 2.2 Clone to Your Machine

```bash
git clone https://github.com/<your-username>/<your-username>.github.io.git
cd <your-username>.github.io
```

---

## Part 3: See It Live — Locally (2 minutes)

Before deploying to the internet, let's see what we have.

### 3.1 Start the Local Server

```bash
hugo server
```

### 3.2 Open Your Browser

Go to **http://localhost:1313**

**You should see a working blog** — with articles, a sidebar, dark mode, and everything.

> **Stop and appreciate this**: In less than 10 minutes, you have a fully functional blog running on your machine. You didn't write HTML, CSS, or JavaScript. You didn't configure a web server. You just ran one command.

### 3.3 Stop the Server

Press `Ctrl+C` in the terminal to stop the local server.

---

## Part 4: Make It Yours (2 minutes)

Let's change something so this blog becomes *yours*.

### 4.1 Edit the Configuration

Open the configuration file:

```bash
nano config/_default/config.toml
```

Find the line that says:

```toml
baseurl = "https://demo.stack.jimmycai.com/"
```

Change it to:

```toml
baseurl = "https://<your-username>.github.io/"
```

Find the `title` line and change it to something like:

```toml
title = "My Awesome Blog"
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

### 4.2 Preview Your Changes

Run `hugo server` again and refresh your browser. The title has changed.

---

## Part 5: Push to GitHub — Watch the Magic (2 minutes)

Now for the exciting part: **push to GitHub and watch automation deploy your site to the internet**.

### 5.1 Commit and Push

```bash
git add .
git commit -m "Initial commit: my blog is alive"
git push origin main
```

### 5.2 Configure GitHub Pages to Use Actions

1. Go to your repository on GitHub: `https://github.com/<your-username>/<your-username>.github.io`
2. Click **Settings** → **Pages** (in the left sidebar)
3. Under **"Build and deployment"** → **Source**, select **"GitHub Actions"**
4. The change is immediate — no Save button needed

### 5.3 Watch the Automation

1. Click the **Actions** tab at the top of your repository
2. You will see a workflow running — it might be called "Deploy Hugo site to Pages" or similar
3. Wait for the green checkmark ✓

### 5.4 Visit Your Live Blog

Open your browser and go to:

**`https://<your-username>.github.io`**

**Your blog is now live on the internet.** Anyone in the world can visit it.

---

## Part 6: What Just Happened? (The "Why" — 1 minute)

You just experienced **automated deployment** without understanding how it works. That's intentional. Now, let's peek behind the curtain:

### 6.1 The Workflow File

Look at this file in your repository:

```bash
cat .github/workflows/gh-pages.yml
```

This YAML file tells GitHub what to do when you push code. In plain English, it says:

> "Whenever someone pushes to the `main` branch, spin up a fresh Ubuntu machine, install Hugo, run `hugo` to build the website, and deploy the result to GitHub Pages."

### 6.2 The Trigger

The workflow is triggered by `push` events to the `main` branch. Every time you `git push`, GitHub Actions runs the workflow automatically.

### 6.3 The Build

The workflow uses a GitHub-provided action to set up Hugo, then runs `hugo` to generate the static HTML files from your Markdown content and Stack theme templates.

### 6.4 The Deploy

The workflow uses the `peaceiris/actions-gh-pages` action to deploy the generated `public/` folder to the `gh-pages` branch, which GitHub Pages serves.

---

## Summary: What You Learned

| Concept | What You Did | What It Means |
|---------|--------------|---------------|
| **Static Site Generator** | Ran `hugo server` | Hugo converts Markdown → HTML |
| **Git** | `git clone`, `git push` | Version control + remote hosting |
| **GitHub Actions** | Pushed code → watched workflow run | Automation: build + deploy on every push |
| **GitHub Pages** | Visited `username.github.io` | Free static hosting |

---

## Your Homework (For Next Class)

1. **Break something**: Edit `config/_default/config.toml` and change `baseurl` to `https://wrong-url/`. Push it. Watch what happens in Actions. Fix it. Push again.

2. **Modify content**: Find `content/posts/` and edit one of the existing Markdown files. Change the title, add a paragraph. Push and see your changes live.

3. **Explore**: Look at the `config/` folder. What other settings can you find? What does `params.toml` control?

---

## Key Insight

**You don't need to understand everything to get started.** The "top-down" approach means:

1. **First**, see the *result* (live website)
2. **Then**, experiment with *changes* (edit config, break things)
3. **Finally**, understand the *mechanism* (GitHub Actions, Hugo, Git)

This is the opposite of traditional teaching (learn Git → learn YAML → learn Hugo → get a website). Which one feels more motivating?

---

*"The important thing is not to stop questioning." — Albert Einstein*
