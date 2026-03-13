# CLAUDE.md — jimmyislive.dev

This is a personal blog built with Hugo and deployed to Netlify via GitHub Actions.
Any push to the main branch triggers an automatic build and deploy to https://jimmyislive.dev.

## Project Structure

```
.
├── content/
│   ├── posts/        # Blog posts (Markdown)
│   └── books/        # Book reviews/reading list (Markdown)
├── static/
│   └── images/       # Images referenced in posts
├── themes/           # Hugo theme (Tale)
├── layouts/          # Any custom layout overrides
├── config.toml       # Hugo site configuration
└── netlify.toml      # Netlify build config
```

## Content Conventions

### Blog Posts
- All posts live in `content/posts/` as `.md` files
- Filename format: `slug-style-name.md` (lowercase, hyphen-separated)
- Every post requires front matter at the top:

```yaml
---
title: "Your Post Title"
date: YYYY-MM-DD
tags: ["tech", "ai"]   # use lowercase tags
draft: false
---
```

- Tags in use: `tech`, `ai`, `books`, `finance`, `general`
- Keep the tone conversational and first-person — this is a personal blog, not a corporate publication
- Posts are opinionated — it's fine to share a point of view
- Avoid excessive headers inside posts; prefer flowing prose

### Books
- Book entries live in `content/books/` as `.md` files
- Include author, a brief description, and personal takeaways

## Hugo-Specific Rules

- Do NOT modify anything inside `themes/` — use `layouts/` overrides instead
- Use Hugo shortcodes for any special formatting (e.g. code blocks, callouts)
- Internal links should use Hugo's `relref` shortcode: `[link text]({{< relref "posts/my-post" >}})`
- Images go in `static/images/` and are referenced as `/images/filename.png`
- Run `hugo server` locally to preview before pushing

## Deployment

- **Branch:** `main` — all pushes trigger a Netlify build automatically
- **Build command:** `hugo --minify`
- **Publish directory:** `public/`
- Do NOT commit the `public/` directory — Netlify builds it
- Do NOT commit `.hugo_build.lock`

## When Helping With This Repo

- When drafting a new post, always create it in `content/posts/` with correct front matter
- Match the existing writing style: concise, thoughtful, first-person, technically grounded
- When fixing formatting issues, prefer minimal changes — don't restructure posts unnecessarily
- Never change the theme files directly
- If adding images, place them in `static/images/` and use the correct Hugo path
- Always validate that front matter is well-formed YAML before committing
- When in doubt about structure, look at existing posts in `content/posts/` as reference


