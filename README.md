# walat.eu

Personal professional website for Dariusz Walat - Senior Software Engineer specializing in AI-augmented development.

## Development

### Run Development Server

Start the Hugo development server with hot reload:

```bash
hugo server --bind 0.0.0.0 --buildDrafts --buildFuture --disableFastRender
```

The site will be available at:
- Local: `http://localhost:1313/`
- Network: `http://192.168.x.x:1313/` (for mobile testing on same network)

Changes to content, layouts, and config will trigger automatic reload in the browser.

Options:
- `--bind 0.0.0.0` - Make server accessible on local network (for mobile testing)
- `--buildDrafts` (`-D`) - Include draft content in the build
- `--buildFuture` - Include content with future publication dates
- `--disableFastRender` - Full rebuild on changes (ensures hot reload works correctly)
- `--port 1313` - Specify port (default is 1313)

### Build for Production

```bash
hugo
```

The static site will be generated in the `public/` directory.

### Create New Content

Create a new blog post:
```bash
hugo new content/blog/my-post-title.md
```

Create a new page:
```bash
hugo new content/page-name.md
```

## Configuration

Site configuration is in `hugo.toml`. Key settings:

### Languages
- **English** (default): Base URL `/`
- **Polish**: URL prefix `/pl/`

### URL Structure
- English: `https://walat.eu/`
- Polish: `https://walat.eu/pl/`

### Language Switcher
- Only shown when translation exists for current page
- Configured in `layouts/_default/baseof.html`
- Uses Hugo's `.IsTranslated` check

### Site Parameters
- `baseURL` - Production URL (https://walat.eu/)
- `title` - Site title
- `[params]` - Custom parameters (email, location, social links)
- `[menu]` - Navigation menu items (language-specific)
- `[languages]` - Bilingual configuration

## Content Guidelines

### Frontmatter for Articles

```yaml
---
title: "Article Title"
subtitle: "Optional Subtitle"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["Series Name"]  # Optional, for series articles
series_order: 1          # Optional, for ordering within series
tags: ["tag1", "tag2"]
description: "Article description for SEO"
---
```

### Series Articles

Articles in a series should:
1. Include `series: ["Series Name"]` in frontmatter
2. Include `series_order: N` for proper ordering
3. Be placed in `content/series/series-slug/` directory
4. Have a `_index.md` file in the series directory as the landing page

## Features

### Bilingual Support
- English as canonical language
- Polish translations supported
- Automatic language switcher (only when translation exists)
- URL structure: `/` for English, `/pl/` for Polish

### Professional Design
- Clean, readable typography
- Mobile-responsive layout
- Syntax highlighting for code blocks
- Professional color scheme optimized for content

### SEO & Social
- OpenGraph meta tags
- Twitter Card support
- Sitemap generation
- RSS feeds

### Series Navigation
- Automatic series navigation on article pages
- Series landing pages with article lists
- Ordered articles within series

## Adding Polish Translations

To add a Polish translation for existing content:

1. Create the Polish version in `content/` directory with language code:
   ```bash
   # For a page
   content/about.pl.md

   # For a series article
   content/series/ai-agents-in-practice/article-name.pl.md
   ```

2. Use same frontmatter as English version
3. Translate the content
4. Language switcher will appear automatically

## Technical Stack

- **Hugo**: Static site generator (v0.151.2+)
- **Markdown**: Content format
- **CSS**: Embedded in `baseof.html` (no external dependencies)
- **Deployment**: Static files in `public/` directory

## Author

**Dariusz Walat**
- Email: dariusz@walat.eu
- LinkedIn: [linkedin.com/in/dariusz-walat](https://www.linkedin.com/in/dariusz-walat)
- Location: Poland (EU Timezone)

---

Built with [Hugo](https://gohugo.io)
