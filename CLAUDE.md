# CLAUDE.md — Nail Atelier LUMIÉ Landing Page

## Project Overview

This is a **single-file static landing page** for "Nail Atelier LUMIÉ | ルミエ", a Japanese nail salon. The entire site lives in one `index.html` file (~1.6 MB, large due to base64-embedded images). There is no build step, no npm, and no framework — pure HTML5/CSS3/Vanilla JS.

## Repository Structure

```
lumiere-lp/
├── index.html    # Entire site: CSS + HTML + JS in one file
└── CLAUDE.md
```

## File Layout Inside `index.html`

| Lines | Content |
|-------|---------|
| 1–9 | `<head>` — meta tags, Google Fonts link |
| 10–1027 | `<style>` — all CSS |
| 1031–1276 | `<body>` — all HTML sections |
| 1277–1337 | `<script>` — all vanilla JS |

## Design System

All colors are CSS custom properties defined in `:root`:

```css
--pink-deep:  #c8637a   /* primary CTA, headings */
--pink-mid:   #e8929f   /* borders, accents */
--pink-light: #f5c6cf   /* decorative lines */
--pink-pale:  #fdf0f2   /* section backgrounds */
--pink-blush: #fce8ec   /* card backgrounds */
--cream:      #fdfaf8   /* page background */
--text-dark:  #3a2a2d   /* body text */
--text-mid:   #7a5a60   /* secondary text */
--text-light: #b08a90   /* captions, notes */
--line-green: #06C755   /* LINE brand (unused in code) */
--gold:       #c9a96e   /* accent */
```

**Always use these variables — never hardcode hex colors.**

### Typography

- `Cormorant Garamond` (serif, weights 300/400/italic) — brand logo, prices, decorative italic accents
- `Noto Sans JP` (sans-serif, weights 300/400/500) — all Japanese body copy

## Page Sections (in DOM order)

| Anchor | Class | Type | Notes |
|--------|-------|------|-------|
| — | `.header` | Fixed nav | Hamburger on mobile |
| — | `.section-hero` | Image-insert | PC + SP `<picture>` variants |
| — | `.line-cta-wrap` | CTA | LINE booking button (×3) |
| `#problem` | `.section-problem` | Code-generated | Problem cards + solution tagline |
| `#concept` | `.section-image` | Image-insert | Concept image placeholder |
| `#gallery` | `.section-gallery` | Code-generated | 3-col photo grid |
| `#menu` | `.section-menu` | Code-generated | Pricing cards |
| `#voice` | `.section-image` | Image-insert | Testimonial image |
| `#line` | inline | Image-insert | Full-width LINE CTA banner |
| `#access` | `.section-image` (footer) | Image-insert | Map/info footer |

### Two Section Types

**Code-generated** (`#problem`, `#gallery`, `#menu`): Content lives directly in HTML tags. Edit text and structure in the markup.

**Image-insert** (hero, concept, voice, `#line`, `#access`): Content is a base64-encoded image embedded in a `<picture>` element. Clickable CTAs are transparent `<a>` tags absolutely positioned over the image using percentage-based coordinates.

## External Links

| Service | URL | Usage |
|---------|-----|-------|
| LINE | `https://lin.ee/xxxxx` | **Placeholder** — replace with the real LINE OA URL |
| Instagram | `https://www.instagram.com/lpmaker_mikky?igsh=...` | Footer overlay button |

When updating the LINE URL, there are **6 occurrences** to update: header CTA, three `.btn-line` CTAs in `.line-cta-wrap`, and two transparent overlay anchors in `#line`.

## Responsive Design

Single breakpoint: `@media (max-width: 768px)`

Key mobile changes:
- `.nav` hides; `.menu-toggle` (hamburger) shows
- Hero switches from `.hero-img-pc` to `.hero-img-sp` via `<picture>` / CSS display toggle
- `.problem-cards`, `.menu-cards` → `flex-direction: column`
- `.gallery-grid` → `repeat(2, 1fr)` from `repeat(3, 1fr)`
- Image overlay buttons (LINE banner, footer Instagram) swap between `-pc` and `-sp` variants with different percentage coordinates

## Animations & Effects

All animations are CSS-only or driven by `IntersectionObserver`.

**Scroll reveal**: Add class `fade-in` to any element. JS adds `.visible` when the element enters the viewport. Use `delay-1` / `delay-2` / `delay-3` for staggered children.

```html
<div class="fade-in delay-2">…</div>
```

**Hero decorations**: `.sparkle-cross`, `.sparkle-dot`, `.heart` — positioned with inline CSS custom properties (`--duration`, `--delay`, `--size`). Do not remove the `.sparkle-container` div.

**Named keyframes**: `sparkleTwinkle`, `sparklePop`, `floatHeart`, `linePulse`, `cardShine`, `logoGlow` — defined in the `<style>` block. Do not rename them; they are referenced by both CSS class rules and inline `animation` declarations.

## JavaScript Patterns

All JS is vanilla, in a single `<script>` block at the end of `<body>`:

- **Scroll-to-top button**: Toggles `.visible` on `#scrollTop` when `window.scrollY > 400`.
- **IntersectionObserver (fade)**: Watches all `.fade-in` elements; adds `.visible` at `threshold: 0.15`.
- **Gallery stagger**: Separate observer for `.gallery-item` at `threshold: 0.1`.
- **Problem card stagger**: Separate observer for `.problem-card` at `threshold: 0.15`.
- **Mobile nav toggle**: Shows/hides `.nav` with `style.cssText`; closes on any nav link click.

When adding new scroll-animated sections, follow the same observer pattern — create a new `IntersectionObserver`, observe the elements, and `unobserve` after first trigger.

## How to Update Content

### Text content (problem/gallery/menu sections)
Edit the inner text/HTML of the relevant elements directly in `index.html`.

### Pricing cards (`.menu-card`)
Prices use `Cormorant Garamond` via the `.menu-price` class. The `<sup>` tag renders the yen symbol and decimal styling.

### Images (image-insert sections)
Replace the base64 data URI inside `<img src="data:image/jpeg;base64,...">`. For hero and some other sections, there are separate PC and SP `<source>` / `<img>` tags inside `<picture>` — update both.

### LINE CTA overlay buttons (`#line` section)
Position is controlled by percentage values on `.line-img-btn-pc` and `.line-img-btn-sp`. If the background image changes and the button area moves, adjust these values:
```css
.line-img-btn-pc { left: 2.1%; top: 71.9%; width: 52.8%; height: 13.5%; }
.line-img-btn-sp { left: 5.3%; top: 81.3%; width: 89.3%; height: 6.3%; }
```

## Development Workflow

No build tools. Open `index.html` directly in a browser, or serve it with any static server:

```bash
python3 -m http.server 8000
# or
npx serve .
```

### Git Branches

- `main` — production
- Feature branches: `claude/<description>-<id>` pattern

### Commit Convention

Commits in this repo use simple descriptive messages (no conventional commits prefix).

## Key Conventions for AI Assistants

1. **Never split into multiple files** unless explicitly asked. The single-file structure is intentional.
2. **Always use CSS variables** from `:root` for colors — never hardcode hex values.
3. **Preserve all six LINE URL occurrences** when the URL is updated.
4. **Respect the two-section-type distinction**: code-generated sections vs. image-insert sections have fundamentally different editing workflows.
5. **Do not add a JavaScript framework or bundler** — this is a zero-dependency static page by design.
6. **Maintain Japanese text** — the page is `lang="ja"` and all copy is in Japanese.
7. **Test both PC and mobile** — the single 768 px breakpoint affects layout, image display, and overlay button coordinates.
8. **Base64 images make the file large** — avoid unnecessary reads/writes of the full file; use `offset`/`limit` or `grep`/`sed` for targeted edits.
