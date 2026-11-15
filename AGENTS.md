# AGENTS.md

## Project Overview

This is a Hugo static site generator project for a technology blog called "Coding Overhead" (coding-overhead.com). The site covers topics related to GNU/Linux, DevOps, Platform Engineering, programming, and useful utilities.

The site is multilingual (Hugo multilingual mode): English is the default language at `/`, Russian is at `/ru/`. Russian posts are marked by the `.ru` file name suffix (`index.ru.md`).

## Key Commands

### Building and Development
```bash
# Build the static site
hugo

# Start development server with live reload and drafts
hugo server -D --bind 0.0.0.0

# Start development server without drafts
hugo server

# Build for production (outputs to public/)
hugo --minify

# Clean generated resources and public directory
rm -rf public/ resources/_gen/
```

### Deployment
```bash
# Deploy to AWS S3 (requires AWS CLI configuration)
hugo deploy --target production

# Build and deploy in one command
hugo --minify && hugo deploy --target production
```
The site is configured to deploy to AWS S3 (s3://coding-overhead.com) as specified in hugo.yaml deployment configuration.

### Content Management
```bash
# Create new post
hugo new posts/YYYY/[post-name]/index.md

# Create new post in Russian
hugo new posts/YYYY/[post-name]/index.ru.md
```

## Site Architecture

### Theme Structure
- Uses custom theme `vng-blue` (`github.com/ismd/hugo-theme-vng-blue`), imported as a Hugo module via `module.imports` in `hugo.yaml`
- Theme supports responsive design with configurable home page layouts
- Includes social media integration and custom styling

### Content Organization
- **Content**: All content in `content/` directory
- **Posts**: Blog posts in `content/posts/YYYY/` with subdirectories per post
- **Pages**: Static pages like About in `content/about.md`
- **Assets**: Images and media files in `assets/` and `static/`

### Content Format
- All content is Markdown: `.md` files with YAML front matter
- Org mode (`.org`) is not used; Hugo 0.166+ blocks it by default

### Configuration
- Main config: `hugo.yaml` (YAML format)
- Theme configuration includes:
  - Social media links (GitHub, LinkedIn, YouTube, Twitch)
  - Custom analytics (Yandex.Metrika)
  - Home page event positioning
  - Languages: English (`en`, default, served at `/`) and Russian (`ru`, served at `/ru/`), defined under `languages` in `hugo.yaml`
  - Language-specific params (`description`, `homeToBlogText`, `mailMeText`, `custom_script_inline` with giscus `data-lang`) live under `languages.<lang>.params`
  - Main menu entries are translated by their `identifier` via `i18n/<lang>.toml`
  - `markup.goldmark.duplicateResourceFiles: true` is required: without it Hugo publishes bundle resources only with the English page, so Russian-only bundles (`index.ru.md` alone) lose `cover.png` and other files

### Key Features
- **Categories and Tags**: Organized content taxonomy
- **Custom Home Page**: Configurable event sections and logos
- **Social Integration**: Links to various social platforms
- **Analytics**: Yandex.Metrika tracking
- **Responsive Design**: Mobile-friendly layout
- **HiDPI Support**: High-resolution image handling

## Content Creation Guidelines

### Front Matter Structure
Standard Markdown posts use YAML front matter:
```yaml
---
title: "Post Title"
summary: "Brief description"
categories:
  - "Category Name"
tags:
  - tag1
  - tag2
date: 2024-01-01T12:00:00
draft: false
---
```

### Post Language
- New posts are written in English (`index.md`)
- Posts in Russian stay in the same `content/posts/` tree, with the `.ru` suffix: `index.ru.md`. Do not prefix their titles with `[RU]`
- A translation of a post goes into the same page bundle (`index.md` + `index.ru.md`, e.g. `posts/2026/keenetic-dns-records/`) and shares its resources (`cover.png`)
- Site pages also have Russian versions: `content/_index.ru.md`, `content/posts/_index.ru.md`, `content/about.ru.md`. Keep them in sync with the English ones

### Summary Style
- Write `summary` in headline style, without a trailing period (in any language)
- In English summaries, also omit the leading article (`A`/`An`/`The`)
- Say what the post covers instead of repeating the title, e.g. `summary: "Quick reference for core Python: data types, loops, functions, classes, exceptions, and more"`

### Image Handling
- Place images in post directories alongside content
- Theme supports HiDPI displays - provide double resolution images
- Home page images: `home-logo-top.png` (1600x530), `home-logo-bottom.png` (2080x470)
- Sidebar logo: minimum 440x440px

### Categories and Tags
- Categories used for major topic groupings (e.g., "Hyprland Setup from Scratch", "Security")
- Tags for specific technologies (linux, hyprland, etc.)

## Theme Customization

The vng-blue theme can be customized through:
- `hugo.yaml` parameters section
- Custom CSS in theme assets
- Logo replacements in `assets/` directory
- Social media configuration in params.social

## Development Notes

- Generated files in `public/` directory (ignored in git)
- Resources cache in `resources/_gen/` (can be cleared if needed)
- Theme is a Hugo module, not a git submodule; its version is pinned in `go.mod` / `go.sum`
- No package.json or Node.js dependencies - pure Hugo project
- Site content is in English; some posts are in Russian (`index.ru.md`, published under `/ru/`)
- Yandex.Metrika analytics configured in hugo.yaml

## Theme Module Management

The theme is managed as a Hugo module. Hugo modules require Go; the theme requires Hugo extended 0.158.0+.
```bash
# Update theme to the latest version
hugo mod get -u github.com/ismd/hugo-theme-vng-blue

# Remove unused entries from go.mod / go.sum
hugo mod tidy

# Show the theme version currently in use
hugo mod graph
```
