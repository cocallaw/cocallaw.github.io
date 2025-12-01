# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Jekyll-based blog using the **Chirpy theme** (v7.4.1) - a minimal, responsive, and feature-rich Jekyll theme for technical writing. The repository contains both the theme source code and blog content.

## Required Dependencies

- **Ruby** ~3.1
- **Bundler** (Ruby gem manager)
- **Node.js** and npm
- **Jekyll** ~4.3
- Git

## Common Development Commands

### Setup and Installation

```bash
# Install Ruby dependencies
bundle install

# Install Node.js dependencies and build assets
npm install
npm run build
```

### Development Server

```bash
# Start Jekyll development server (default)
bundle exec jekyll serve

# Or use the convenience script
bash tools/run.sh

# Start with production environment
bash tools/run.sh --production

# Start with custom host
bash tools/run.sh --host 0.0.0.0
```

The local server runs at <http://127.0.0.1:4000> by default.

### Building

```bash
# Build for production
JEKYLL_ENV=production bundle exec jekyll build

# Build JavaScript and CSS assets
npm run build

# Build CSS only (PurgeCSS optimization)
npm run build:css

# Build JavaScript only (Rollup bundling)
npm run build:js
```

### Linting and Testing

```bash
# Run all tests (JavaScript and SCSS linting)
npm test

# Lint JavaScript only
npm run lint:js

# Lint SCSS only
npm run lint:scss

# Auto-fix SCSS linting issues
npm run lint:fix:scss

# Build and test the site (includes HTML proofing)
bash tools/test.sh
```

### Development Workflow

```bash
# Watch JavaScript files for changes during development
npm run watch:js
```

## Architecture and Structure

### Directory Layout

- **`_posts/`** - Blog post markdown files (format: `YYYY-MM-DD-title.md`)
- **`_drafts/`** - Draft posts (not published)
- **`_tabs/`** - Static pages (About, Archives, Categories, Tags)
- **`_layouts/`** - HTML layout templates (default, post, page, home, etc.)
- **`_includes/`** - Reusable HTML components (header, sidebar, TOC, comments, etc.)
- **`_sass/`** - SCSS stylesheets organized by purpose:
  - `abstracts/` - Variables, mixins, functions
  - `base/` - Base styles and resets
  - `components/` - UI components (buttons, cards, etc.)
  - `layout/` - Layout-specific styles (sidebar, topbar, footer)
  - `pages/` - Page-specific styles
  - `themes/` - Theme variations (light/dark mode)
- **`_javascript/`** - JavaScript source modules
  - `modules/` - Shared utility modules
  - `pwa/` - Progressive Web App functionality
  - Page-specific scripts: `commons.js`, `home.js`, `post.js`, etc.
- **`_data/`** - Jekyll data files (YAML configuration)
  - `locales/` - Internationalization strings
  - `contact.yml` - Social contact links configuration
  - `origin/cors.yml` - CDN and static asset origins
- **`_plugins/`** - Custom Jekyll plugins
- **`assets/`** - Static assets (images, fonts, compiled CSS/JS output)
- **`tools/`** - Build and maintenance scripts
- **`docs/`** - Documentation (CHANGELOG, CONTRIBUTING, etc.)

### Key Configuration Files

- **`_config.yml`** - Main Jekyll site configuration (title, URL, timezone, theme settings, plugins)
- **`package.json`** - Node.js dependencies and build scripts
- **`Gemfile`** - Ruby gem dependencies
- **`rollup.config.js`** - JavaScript bundling configuration (ES6 → ES5 via Babel)
- **`purgecss.js`** - CSS optimization configuration (removes unused Bootstrap CSS)

### Build Pipeline

1. **CSS Processing**: PurgeCSS extracts and optimizes Bootstrap CSS → `_sass/vendors/_bootstrap.scss`
2. **JavaScript Bundling**: Rollup bundles ES6 modules via Babel → `assets/js/dist/*.min.js`
   - Creates minified bundles for: commons, home, categories, page, post, misc, theme
   - PWA service worker and app manifest
   - Jekyll frontmatter is injected into JS files for proper routing
3. **Jekyll Build**: Compiles Markdown, Liquid templates, and Sass → `_site/` directory
4. **HTML Proofing**: Validates generated HTML (via `html-proofer` gem)

### Front Matter Structure

Blog posts use YAML front matter with these key fields:

```yaml
---
title: Post Title
description: Post description for SEO
author: author_name
date: YYYY-MM-DD HH:MM:SS +TIMEZONE
categories: [Category, Subcategory]
tags: [tag1, tag2]
pin: true                    # Pin to homepage
media_subpath: '/path'       # Base path for post images
image:                       # Social preview image
  path: /path/to/image.jpg
  alt: Image description
---
```

### JavaScript Module Organization

JavaScript is organized by page type and loaded conditionally:

- **commons.js** - Shared utilities (loaded on all pages)
- **home.js** - Homepage-specific functionality
- **post.js** - Blog post page features (TOC, code highlighting)
- **categories.js** - Category page interactions
- **page.js** - Generic page functionality
- **misc.js** - Miscellaneous utilities
- **theme.js** - Dark/light mode theme switcher (exported as `Theme` global)
- **pwa/app.js** & **pwa/sw.js** - Progressive Web App functionality

### Theme System

The theme supports light/dark modes with automatic OS preference detection. Theme preference is stored in localStorage. The theme switcher is in the sidebar.

## Development Notes

### Post Permalinks

Default permalink structure: `/posts/:title/` (configured in `_config.yml`). Do not modify without updating all internal links.

### CDN Configuration

Static assets use CDN defined in `_config.yml` (`cdn` field) and `_data/origin/cors.yml`. For self-hosting, refer to [chirpy-static-assets](https://github.com/cotes2020/chirpy-static-assets).

### Comments Systems

Supports Disqus, Utterances, and Giscus. Configure in `_config.yml` under `comments` section.

### Commit Conventions

This project uses [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` - New features
- `fix:` - Bug fixes
- `perf:` - Performance improvements
- `refactor:` - Code refactoring
- `docs:` - Documentation changes
- `chore:` - Build process, dependencies, etc.

Commits are enforced via Husky and commitlint.

### Ignored Files

When indexing this codebase, note that `_site/` (build output), `node_modules/`, and `.sass-cache/` should be ignored.

## Important Constraints

- Ruby version must be ~3.1 (specified in gemspec)
- Node.js is required for asset building (ES6 → ES5 transpilation)
- Jekyll 4.3+ is required
- The production release branch is `production` (not `main` or `master`)
- Build output directories (`_site/`, `assets/js/dist/`, `_sass/vendors/`) are gitignored and regenerated on each build
