# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based technical blog using the Chirpy theme, hosted at https://landelyse.github.io. The blog focuses on iOS development insights, Swift, UIKit, SwiftUI architecture design, and clean code principles.

## Development Commands

### Local Development
```bash
# Start development server
bash tools/run.sh

# Start with custom host
bash tools/run.sh -H 0.0.0.0

# Start in production mode
bash tools/run.sh --production
```

### Build and Test
```bash
# Build and test the site
bash tools/test.sh

# Build with custom config
bash tools/test.sh -c "_config.yml,_config_local.yml"
```

### Asset Management
```bash
# Build all assets (CSS and JS)
npm run build

# Build CSS only
npm run build:css

# Build JS only (production)
npm run build:js

# Watch JS files for development
npm run watch:js

# Lint SCSS files
npm run lint:scss

# Lint and auto-fix SCSS files
npm run lint:fix:scss

# Run tests (SCSS linting)
npm test
```

### Jekyll Commands
```bash
# Install dependencies
bundle install

# Build site for production
JEKYLL_ENV=production bundle exec jekyll build

# Serve locally
bundle exec jekyll serve -l

# Clean build files
bundle exec jekyll clean
```

## Architecture

### Key Directories
- `_posts/`: Blog posts in Markdown format
- `_config.yml`: Main Jekyll configuration
- `_javascript/`: Source JavaScript files (ES6+)
- `_sass/`: SCSS stylesheets organized by component
- `assets/`: Static assets (images, compiled CSS/JS)
- `_site/`: Generated site output (Git ignored)
- `tools/`: Development scripts

### Build Process
- JavaScript is built using Rollup with Babel transpilation
- SCSS is compiled and purged using PurgeCSS
- Assets are minified for production
- PWA service worker is generated automatically

### Content Structure
- Posts use frontmatter with categories and tags
- Categories are hierarchical
- TOC is auto-generated for posts
- Korean language content with English configuration
- SEO optimizations included

### Theme Customization
- Based on jekyll-theme-chirpy v7.2.4
- Custom avatar and site branding in `_config.yml`
- Localized for Korean content
- PWA enabled with offline caching

## Development Notes

- The site uses conventional commits for semantic release
- JavaScript modules are in `_javascript/modules/`
- PWA files are in `_javascript/pwa/`
- Style follows SCSS component architecture
- HTML proofer runs on builds to validate links
- Supports math expressions via MathJax
- Mermaid diagrams supported
- Comment systems can be configured (Disqus, Utterances, Giscus)