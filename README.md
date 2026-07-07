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

## Deployment

Push to `main` — GitHub Actions builds and deploys to GitHub Pages automatically.

## Requirements

- Hugo (Extended)
- Asciidoctor (`gem install asciidoctor`)

See `/arch/build-guide/` on the live site for full setup instructions.
