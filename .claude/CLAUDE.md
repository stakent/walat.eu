# Hugo Site Development Notes

## Running the development server

Always use this command to start Hugo server so it's accessible on the local network (for mobile testing):

```bash
hugo server --bind 0.0.0.0 --buildDrafts --disableFastRender
```

This allows viewing the site from mobile devices on the same local network.

## Site Positioning and Voice

**This is NOT a blog.** This is an authority site maintained by a professional, for professionals, with authority-building content.

- Never use the word "blog" in content, navigation, or site structure
- Content is: research series, essays, technical documentation, professional positioning
- Voice: authoritative, technical, honest about limitations, builds credibility through demonstrated expertise
- Audience: senior engineers, engineering managers, technical decision-makers

## Content Translation

- English content files use standard names (e.g., `_index.md`)
- Polish content files use `.pl.md` extension (e.g., `_index.pl.md`)
- Use `<!-- FIXME: Translate this content from English version once finalized -->` for content pending translation
