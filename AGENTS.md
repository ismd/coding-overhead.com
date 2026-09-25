# AGENTS.md

## Project Overview

This is a Hugo static site generator project for a technology blog called "Coding Overhead" (coding-overhead.com). The site covers topics related to GNU/Linux, DevOps, Platform Engineering, programming, and useful utilities.

The blog is written in English. Some posts are in Russian; their titles are prefixed with `[RU]`.

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
```

## Site Architecture

### Theme Structure
- Uses custom theme `vng-blue` located in `themes/vng-blue/`
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
  - Site language: English (single-language site, no Hugo multilingual mode)

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
- New posts are written in English
- Posts in Russian stay in the same `content/posts/` tree with regular file names (`index.md`)
- Prefix the title of every Russian post with `[RU]`, e.g. `title: "[RU] Шифрование устройств в Linux"`

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
- Categories used for major topic groupings (e.g., "Настройка окружения Hyprland от начала до конца")
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
- Theme is included as git submodule at `themes/vng-blue/`
- No package.json or Node.js dependencies - pure Hugo project
- Site content is in English; some posts are in Russian (marked with `[RU]` in the title)
- Yandex.Metrika analytics configured in hugo.yaml

## Git Submodule Management

The theme is managed as a git submodule:
```bash
# Update theme submodule
git submodule update --remote themes/vng-blue

# Initialize submodules (if cloning fresh repo)
git submodule update --init --recursive
```
