# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based GitHub Pages site for "Charlie and Phillip's Website" (charlieandphillip.com), a personal blog and portfolio showcasing posts, games, and projects created by Charlie and Phillip Warren.

## Development Commands

All Jekyll commands must be run from the `docs/` directory:

```bash
cd docs

# Install dependencies (first time setup)
bundle install

# Serve the site locally with live reload
bundle exec jekyll serve

# Build the site (output to _site/)
bundle exec jekyll build
```

The local development server runs at `http://localhost:4000` by default.

## Repository Structure

```
ratherDashing.github.io/
└── docs/                      # Jekyll site root (all work happens here)
    ├── _config.yml            # Site configuration
    ├── _posts/                # Blog posts (markdown files)
    ├── _layouts/              # Custom layout templates
    │   └── home.html          # Custom home page layout
    ├── assets/                # Static assets (images, etc.)
    ├── Gemfile                # Ruby dependencies
    ├── index.markdown         # Home page
    └── about.markdown         # About page
```

## Key Configuration Details

**Site Configuration** (`_config.yml`):
- Theme: Minima (~> 2.5)
- GitHub Pages gem version: ~> 217
- `future: true` - allows posts with future dates to be published
- `kramdown.hard_wrap: true` - preserves line breaks in markdown
- Title: "Charlie and Phillip's Website"
- URL: https://charlieandphillip.com

**Custom Layout**: The site overrides the default Minima home layout at `_layouts/home.html` to customize the homepage appearance.

## Blog Post Conventions

Posts are located in `docs/_posts/` and follow Jekyll naming convention:
- Format: `YYYY-MM-DD-title-slug.markdown`
- Front matter includes: `layout`, `title`, `date`, `categories`

**Post Types**:
1. **Standard blog posts**: Simple markdown content with images
2. **Interactive posts**: Embedded React applications (e.g., TicTacGrow game)

**Interactive Posts Architecture**:
Some posts include embedded React applications using CDN-loaded React:
- React and ReactDOM loaded via unpkg.com
- Babel standalone for JSX transformation
- Inline `<script type="text/babel">` for React components
- Inline `<style>` blocks for component styling
- Must use Jekyll raw tags (`{% raw %}...{% endraw %}`) around React code to prevent Liquid template processing

Example structure for interactive posts:
```markdown
---
layout: post
title: "Post Title"
---

<div id="app-root"></div>

<script src="https://unpkg.com/react@18/..."></script>
<script src="https://unpkg.com/react-dom@18/..."></script>
<script src="https://unpkg.com/@babel/standalone/..."></script>

<style>
/* Component styles */
</style>

<script type="text/babel">
// {% raw %}
// React component code
// {% endraw %}
</script>
```

## Important Notes

- The site is deployed via GitHub Pages, which builds automatically on push to the main branch
- Local builds generate output to `_site/` (gitignored)
- When creating new posts with React components, always wrap JSX code in Jekyll raw tags to prevent template processing conflicts
- The site uses future dating for posts, so dates can be set in the future for scheduled content
