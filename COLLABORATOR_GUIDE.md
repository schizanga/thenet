# Collaborator Guide

This guide records the site changes and conventions established during the recent library, newsletter, footer, and typography work.

## Project Overview

This is a Hugo site using the Bear Blog theme. The site-specific implementation lives under `layouts/`, data-driven library entries live under `data/library/`, and local font files live under `fonts/`.

Build and validate the site with:

```sh
hugo --gc --minify
```

Generated files in `public/` are build output. Update source files, then rebuild; do not edit generated pages directly.

## Social Link Previews

Social preview metadata is emitted by `layouts/partials/seo_tags.html`, which uses Hugo's built-in Open Graph and Twitter card templates. The default site title, description, and image are configured in `hugo.toml`.

The current default description is `Sam Chizanga's writing, resources, and offerings.`. Generated pages include `og:title`, `og:description`, `og:url`, `og:image`, `twitter:card`, `twitter:title`, `twitter:description`, and `twitter:image`.

The fallback preview image is `static/images/share.webp`, copied from the site's existing logo asset. It is currently a square logo image; for best results, replace it with a 1200x630 JPG, PNG, or WebP image that remains legible when cropped on social platforms.

Individual pages can override the fallback with front matter:

```toml
title = "Your post title"
description = "The description shown in the social preview."
images = ["/images/your-post-image.jpg"]
```

Store page-specific images in `static/images/`. After deploying a change, social platforms may continue showing an older cached preview; use the platform's URL inspection or sharing debugger to refresh it.

Because `hugo.toml` defines custom module mounts, it must mount both `static` and `fonts`. Without the `static` mount, files such as `static/images/share.webp` will not be copied to `public/`.

## Library Filters

The library page is implemented in `layouts/_default/library.html`.

The sidebar currently provides:

- Search
- Category checkboxes
- Zodiac sign radio filters
- Planet radio filters
- House radio filters for 1st through 12th houses

Each filter section uses native `<details>` and `<summary>` elements. Category is open by default; the other sections are collapsed to keep the sidebar compact and accessible.

The Library page displays `External links open in a new tab.` because library item links use `target="_blank"`.

Filtering is handled by the JavaScript in `layouts/_default/library.html`. All active filters are combined, so an item must match search, category, zodiac, planet, and house selections to remain visible.

### Adding Library Metadata

Library entries are stored in:

- `data/library/articles.yaml`
- `data/library/calculators-and-platforms.yaml`

Each entry may include:

```yaml
title: "Resource title"
url: "https://example.com"
category: "Articles"
sign: "Virgo"
planet: "Saturn"
house: "7th"
note: "by Author"
```

The `house` value must match one of the filter values: `1st`, `2nd`, through `12th`. The item partial, `layouts/partials/library-item.html`, exposes this metadata through `data-house` and includes it in search text.

## Newsletter Form

The newsletter markup is in `layouts/partials/newsletter-form.html`.

The form intentionally contains only:

- First name
- Email address
- Subscribe button

The previous topic/list checkboxes and their synchronization script were removed. If a real newsletter provider is selected, replace the form's `action="#"` with that provider's endpoint and preserve the existing field names unless the provider requires different names.

Newsletter styles are in `layouts/partials/style.html`. Keep the form responsive by preserving the existing `.newsletter-fields` and `.newsletter-field` structure.

## Footer and Social Links

The footer is implemented in `layouts/partials/footer.html`.

The footer row contains the Hugo Bear credit and three icon links:

- Email: `mailto:hi@samchizanga.com`
- Instagram: `https://www.instagram.com/samchizanga/`
- Threads: `https://www.threads.net/@samchizanga`

Each icon has an `aria-label` and `title`. Update both the URL and accessible label if an account changes. The row layout is controlled by `.footer-row`, `.footer-icons`, `.social-icon`, and `.made-with` in `layouts/partials/style.html`.

A second footer row reserves space for these site links:

- Privacy Policy: `/privacy-policy/`
- Legal Notices: `/legal-notices/`
- General Conditions: `/general-conditions/`

The links are styled by `.footer-legal-links` and wrap on narrow screens. Create the corresponding pages in `content/` before adding policy content.

## Custom Fonts

Font files are stored in `fonts/`:

- `PerfectlyNineties-Regular.ttf` for body text
- `PerfectlyNineties-Italic.ttf` for future italic use
- `NinetiesHeadliner-Regular.ttf` for headings

The `fonts/` directory is published through the Hugo mount in `hugo.toml`:

```toml
[module]
  [[module.mounts]]
    source = "fonts"
    target = "static/fonts"
```

Font declarations and CSS variables are in `layouts/partials/style.html`:

```css
@font-face {
    font-family: "Nineties Headliner";
    src: url("/fonts/NinetiesHeadliner-Regular.ttf") format("truetype");
    font-weight: 400;
    font-style: normal;
    font-display: swap;
}
```

Body text uses `--font-main`, while `h1` through `h6` use `--font-heading`. When adding a new font, register each available weight and style separately, use the correct `truetype` or `woff2` format, and run a production build to confirm the file is emitted.

The stylesheet uses CSS custom properties intentionally. Theme values are defined in `:root` for dark mode and overridden by `body.light-mode` for light mode. Keep colors, form controls, headings, links, and buttons connected to these variables so the theme remains consistent when the toggle is used.

Typography follows an explicit scale: body text is `1rem`; headings use `2.25rem`, `1.75rem`, `1.35rem`, `1.15rem`, `1rem`, and `0.9rem` from `h1` through `h6`. Body text uses `line-height: 1.8` and slight letter spacing, while headings use tighter line height and normal letter spacing.

## Optional Custom Body Partial

The site overrides the theme's `baseof.html`, which calls `custom_body.html`. Keep `layouts/partials/custom_body.html` present even when it is empty so Hugo can resolve the optional hook during 404 and normal page rendering.

## Validation Checklist

After source changes:

1. Run `hugo --gc --minify`.
2. Confirm the build completes without template or partial errors.
3. Check the affected page in the local Hugo server.
4. For library changes, test a filter with a known metadata value such as `7th` house.
5. For font changes, confirm the font files appear in the generated static output.
6. Update this guide when a new site-wide convention is introduced.