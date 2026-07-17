# rocasta.com

Personal blog and creative writing site built with **Hugo** + **PaperMod**.
Content is written in **AsciiDoc**.

## Quick Start

```bash
# Serve with live reload
hugo server -D

# Build to ./public
hugo --minify
```

## Structure

| Path | Content |
|------|---------|
| `content/posts/` | Blog posts (`.adoc`) |
| `content/arch/` | Architecture and build docs |
| `content/about.md` | About page |
| `static/images/` | Post images served at `/images/` |
| `themes/PaperMod/` | Theme submodule |

## Writing a New Post

```bash
# 1. Create the post file
cat > content/posts/YYYY-MM-DD-short-title.adoc << 'EOF'
---
title: "Your Title"
date: YYYY-MM-DD
showToc: true
---

= Your Title

Content here in AsciiDoc.
EOF

# 2. If the post has images, create an image directory
mkdir -p static/images/short-title
# Copy or hard-link images into it
# Reference images in the post with: :imagesdir: /images/short-title

# 3. Preview locally
hugo server -D
# Open http://localhost:1313

# 4. When ready, commit and push
git add content/posts/ static/images/
git commit -m "add post: Your Title"
git push origin main
# GitHub Actions deploys automatically
```

## Deployment

Push to `main` — GitHub Actions builds and deploys to GitHub Pages automatically.

## Requirements

- Hugo (Extended)
- Asciidoctor (`gem install asciidoctor`)

See `/arch/build-guide/` on the live site for full setup instructions.
