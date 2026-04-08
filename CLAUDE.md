# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based GitHub Pages site for "Charlie and Phillip's Website" (charlieandphillip.com), a personal blog and portfolio showcasing posts, games, and projects created by Charlie and Phillip Warren.

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

## Publishing Books from Google Docs

Charlie writes books in Google Docs and exports them as `.zip` files (HTML export). To publish one:

1. **Extract the zip** — it contains an HTML file and an `images/` folder
2. **Copy images** to `docs/assets/images/` with descriptive names (e.g., `sit-cover.png` instead of `image1.png`)
3. **Create a post** in `docs/_posts/` with category `Books`
4. **Convert the HTML to markdown**, preserving Charlie's formatting:
   - Chapter titles as `<div class="chapter-title">` elements (styled at 2em font size)
   - Italic text for thoughts and emphasis using `*...*`
   - Bold/italic/underline for sound effects using `***<u>...</u>***`
   - Monospace/computer text using `<span class="computer-text">...</span>` for gadget screens, search results, and passcodes
   - Horizontal rules (`---`) between chapters
   - The book's front matter (cover, author credits, other books) and back matter (about the author, mission info) should be included
5. **Include these CSS classes** in a `<style>` block at the top of the post:
   - `.book-cover` — centered, max-width 500px
   - `.book-meta` — smaller font for credits
   - `.chapter-title` — 2em font size with top margin
   - `.computer-text` — Consolas/monospace font
   - `.book-title-large` — 2.5em for the title page
6. **Delete the zip** after publishing
7. The Google Docs HTML is a single long line with lots of CSS classes (c0, c1, etc.) and `&ldquo;`/`&rdquo;` entities — these all need to be converted to clean markdown with proper quotes

## Jekyll/Kramdown HTML Gotchas

Kramdown (the markdown renderer used by GitHub Pages) has important quirks when mixing HTML and markdown:

- **Markdown inside HTML blocks is NOT processed.** If you put `**bold**` or `## Heading` inside a `<div>`, it renders as literal text. To fix this, either:
  - Add `markdown="1"` to the HTML element: `<div markdown="1">`
  - Or use raw HTML tags instead (`<strong>`, `<h2>`, `<p>`, `<br>`)
- **Text inside `<div>` blocks has no line breaks.** Plain text in a div runs together. Use `<p>` and `<br>` tags for structure.
- **Backslash escapes can render literally.** `\-` shows as `\-` in the output. Just use the character directly or use HTML entities.
- **Always verify published output with `curl`.** The local markdown preview and the actual Jekyll build can differ significantly. Curl the live page after pushing to check that formatting rendered correctly.
- **Prefer HTML over markdown when inside HTML blocks.** Don't mix markdown syntax into `<div>` sections — use `<strong>`, `<em>`, `<h2>`, `<p>`, `<br>` instead.

## Important Notes

- The site is deployed via GitHub Pages, which builds automatically on push to the main branch
- There is no local Jekyll/bundle setup — just push and verify on the live site
- When creating new posts with React components, always wrap JSX code in Jekyll raw tags to prevent template processing conflicts
- The site uses future dating for posts, so dates can be set in the future for scheduled content
