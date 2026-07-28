---
title: Github Pages Solution
---

As an agile devops learning/tinker model also document automation tutorial for IT developer or College student level, from the three sample sites (2011 nanoc, 2023 Sphinx , 2023 Hugo + Stack theme) Investigation
Now how to make the plan

1. just github pages template works, zero github action, workflow, docker,  build, deploy
2. Is it best for github.io static pages
3. lab env. On native Archlinux
4. e.g. learning/trial doc into markdown, git push to github pages then https://user.github.io/xyz/ to share
5. any details, steps etc.?

![diagram](zero_build_github_pages_pipeline.svg)
Good news: this question has a clean answer once you connect it to what you already researched.

**The core finding: none of your three sample sites satisfy requirement #1 natively.** nanoc needs `nanoc compile` to run somewhere. Sphinx needs `sphinx-build`. Hugo needs the `hugo` binary. GitHub doesn't speak Ruby, Python, or Go for you — so any of those three forces you into either building locally yourself (that's a build step) or writing a GitHub Actions workflow (that's a workflow). Neither fits "zero action, zero workflow, zero build."

There's exactly one static-site tool GitHub Pages will build *for* you with none of that: **Jekyll**. It's the one generator GitHub's own servers know how to run automatically. That's the whole trick — your plan isn't really "pick a tool," it's "use the one tool GitHub already runs behind the scenes."

Here's that pipeline:

The diagram above is the entire mental model: two things happen on your machine (edit, push), two things happen on GitHub's side (build, serve) — and you never touch that middle step yourself.

**The plan, step by step**

*1. Arch Linux setup — genuinely minimal*
```bash
sudo pacman -S git
sudo pacman -S github-cli   # optional, for `gh repo create` from the terminal
sudo pacman -S neovim       # you're already using this from the vim tutorials
```
No Ruby, no `jekyll` gem, no Hugo binary, no Docker. GitHub's servers install and run Jekyll for you — your machine only ever holds markdown files and git.

*2. Create one repo per "lab"*
Matching your `user.github.io/xyz/` example, the simplest pattern is one repo = one experiment, named descriptively (`xyz`, `docker-notes`, `neovim-lab`, whatever). Any public repo with Pages turned on is automatically served at `https://user.github.io/<repo-name>/` — you don't need the special `user.github.io`-named repo at all unless you want something at the bare root domain.
```bash
mkdir xyz && cd xyz
git init
echo "---
title: home
---
# My tinker lab" > index.md
git add . && git commit -m "init"
gh repo create xyz --public --source=. --push
```

*3. Turn on Pages — one-time click, not a script*
In the repo on github.com: Settings → Pages → Build and deployment → Source: **Deploy from a branch** → Branch: `main`, folder `/(root)`. Save. This branch-based Jekyll build is still fully supported, even though GitHub's docs now nudge people toward the Actions-based option for more control. For your stated goal, branch-based is the correct choice, not the legacy one.

*4. (Optional, still zero-build) Pick a look*
Add a `_config.yml` with one line:
```yaml
theme: jekyll-theme-cayman
```
GitHub Pages ships with a fixed set of ready-to-use themes — Cayman, Dinky, Hacker, Leap day, Merlot, Midnight, Minima, Minimal, Modernist, Slate, Tactile, and Time machine — no Gemfile, no local install, just a theme name.

*5. Write, push, share*
```markdown
---
title: "Day 1 - Vim survival mode"
---
# Day 1 — Vim survival mode
Notes from today's practice...
```
```bash
git add . && git commit -m "day 1 notes" && git push
```
Wait about 30–60 seconds, then `https://user.github.io/xyz/` is live.

**Is this "best" for github.io? (your question 2)**
For your exact constraint — zero pipeline, just write and push — yes, this is the objectively correct fit, not just a preference: Jekyll is the only generator GitHub auto-builds. But "best" depends on the goal. Once your labs grow (hundreds of pages, need for Hugo's speed, or Sphinx's cross-reference/API-doc features), you'll outgrow zero-build and want a real pipeline — that's a good "sprint 2" for later, using GitHub Actions to bring back the Hugo or Sphinx setups you already investigated. For a learning/tinker notebook right now, Jekyll wins on friction alone.

**Gotchas worth knowing up front**
- Front matter (the `---` YAML block) is what tells Jekyll "turn this into a page." A `.md` file with no front matter gets copied as a raw file, not rendered into your theme.
- The repo must be public for free Pages (unless you're on a paid GitHub plan).
- Even in "deploy from a branch" mode, GitHub is quietly running an automated workflow called `pages-build-deployment` behind the scenes — you'll actually see a green checkmark under the repo's Actions tab. You didn't write it and don't maintain it, but it's worth knowing it's there when you're checking why a page hasn't updated yet.
- Case sensitivity and trailing slashes matter in paths — `xyz/02` and `xyz/02/` can behave differently.

That's the whole zero-build tier. When you're ready to level up, the natural next step — given you've already dissected nanoc, Sphinx, and Hugo+Stack — is swapping in one of those via a GitHub Actions workflow, which is exactly what turns "deploy from a branch" into "GitHub Actions" in that same Settings → Pages screen.
