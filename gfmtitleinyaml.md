---
title: |
  Github Markdown <br>
  multi-line title <br>
  within yaml header
---

> **Notes**: 
>     Inline code, code snippet, HTML tag 
>   have to be changed into markdown
>   07/30/2026, Text in markdown from Google AI mode

To create a multi-line title in a Markdown YAML header (front matter), use the YAML literal block scalar indicator | followed by a standard HTML `<br>` tag for the line break. [^1] 
## Recommended Syntax

```yaml
---
title: |
  First Line of Title <br>
  Second Line of Title
author: Jane Doe
---
```

## Why This Method Works

* The Pipe Operator (`|`): This Tells YAML to preserve your literal newlines and treat everything indented below it as a single string.
* The `<br>` Tag: Most Markdown parsers (like [GitHub Pages / Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll) and Pandoc) parse the YAML title string directly into an HTML `<h1>` tag jgm/pandoc. Because standard Markdown ignores raw single newlines inside paragraph-like blocks, the HTML `<br>` tag is required to force the visual line break on the rendered page. [^2] 

## Alternative: Title and Subtitle
If your multi-line title is actually a main title followed by a subtitle, it is cleaner to separate them into two distinct YAML keys if your theme supports it:

```yaml
---
title: "My Main Title"
subtitle: "My Subtitle text goes here"
---
```

To help tailor this, let me know:

* What static site generator or tool are you using to render the Markdown? (e.g., Jekyll, Hugo, Astro, or standard GitHub UI)
* Does your current setup output the raw `<br>` text instead of breaking the line?

Knowing this can help determine if your template needs explicit HTML escaping adjustments.

[^1]: [https://microfluidics.utoronto.ca](https://microfluidics.utoronto.ca/gitlab/help/ci/yaml/script.md)  
[^2]: [https://github.com](https://github.com/dbt-labs/dbt-core/issues/5330)
