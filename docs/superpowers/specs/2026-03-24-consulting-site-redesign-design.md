# Design Spec: Consulting Site Redesign
**Date:** 2026-03-24
**Author:** Antonio de la Vega de León
**Status:** Approved

---

## Context

The website at adlvdl.github.io is transitioning from a personal academic page to a consulting business site for ML/cheminformatics services. The current design (GitHub Pages template, Open Sans, teal gradient) reads as generic academic. The new site must communicate professional consulting credibility, include a blog, and adopt a distinctive winter/mathematical aesthetic that feels human-designed.

---

## Design System

### Aesthetic
Winter/cold/mathematical. Precise, restrained, human. Not generic "AI slop." Inspired by scientific computing interfaces and crystallography papers.

### Typography
- **Lexend** (Google Fonts) — headings and body text. Weights 200 (hero names), 300 (body), 400 (UI).
- **JetBrains Mono** (Google Fonts) — labels, tags, navigation, code, mono accents. Weights 300–400.
- No other font families.

**Google Fonts `<link>` tag (add to every page `<head>`):**
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400&family=Lexend:wght@200;300;400&display=swap" rel="stylesheet">
```

### Color Tokens (CSS custom properties)

| Token | Light (`#f4f6fb` bg) | Dark (`#0e1926` bg) | Usage |
|---|---|---|---|
| `--bg` | `#f4f6fb` | `#0e1926` | Page background |
| `--bg-alt` | `#eaecf4` | `#0b1420` | Alternate sections, footer |
| `--bg-card` | `#ffffff` | `#111e2c` | Card surfaces |
| `--border` | `#d0d8e8` | `#162232` | Dividers, borders |
| `--text` | `#0f1a28` | `#d4e0ec` | Primary text |
| `--text-2` | `#3a5068` | `#7a9cb8` | Secondary text |
| `--text-3` | `#7888a0` | `#6a8ea8` | Muted/tertiary — large text or decorative only |
| `--accent` | `#1a4a78` | `#5ab4e0` | Primary accent (links, CTAs, active nav) |
| `--accent-2` | `#2a6aaa` | `#7acce8` | Hover, secondary accent |
| `--ice` | `#5ab4e0` | `#5ab4e0` | Mono tags, highlights (same both modes) |

> **Contrast note:** `--text-3` in dark mode (`#6a8ea8` on `#0e1926`) is ~4.5:1. Restrict usage to decorative labels or large (≥1rem) text only; use `--text-2` for readable body copy.

### Theme Mode
- **Auto-detect** via `prefers-color-scheme: dark`
- **Default** to light if media query not available
- **Toggle button** in nav (◐ icon + current mode label)
- **Persistence** via `localStorage` key `theme`
- **Mechanism:** `data-theme` attribute on `<html>`, set by inline script in `<head>` before body renders (prevents flash)

**Inline theme script (first thing in `<head>`):**
```js
<script>
(function(){
  var s = localStorage.getItem('theme');
  var p = window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
  document.documentElement.setAttribute('data-theme', s || p);
})();
</script>
```

**CSS theme blocks in `css/style.css`:**
```css
:root, [data-theme="light"] { /* light tokens */ }
[data-theme="dark"]          { /* dark tokens */ }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* dark tokens (fallback before JS runs) */ }
}
```

### Animations
Subtle, purposeful. CSS-only.

- **Page load hero:** staggered `fade-up` on hero elements. Tag (0ms) → name (80ms) → h1 (160ms) → body (240ms) → CTAs (320ms). Duration 400ms, `ease-out`, `opacity 0→1` + `transform translateY(12px)→0`.
- **Card hover:** `background-color` transition, 150ms `ease`. No movement.
- **Nav link hover:** `color` transition, 150ms `ease`.
- **Theme toggle:** no animation (instant).
- Use `@media (prefers-reduced-motion: reduce)` to disable all transitions/animations.

---

## File Structure Changes

### New / replaced files
| File | Action |
|---|---|
| `css/style.css` | **New** — replaces `css/external_style.css` |
| `index.html` | **Rewrite** |
| `services.html` | **New** |
| `blog.html` | **New** (blog index) |
| `about.html` | **New** (absorbs research, outreach, teaching content) |
| `publications.html` | **Rewrite** (markup + styles only, keep content) |
| `posts/2025_10_09_fp_comparison.html` | **Restyle** (update `<link>` and nav/footer markup) |

### Retired files
| File | Action |
|---|---|
| `css/external_style.css` | Delete |
| `research.html` | Delete (content migrated to `about.html`) |
| `outreach.html` | Delete (content migrated to `about.html`) |
| `teaching.html` | Delete (content migrated to `about.html`) |

### Unchanged
- `visualizations/` — left as isolated standalone pages, not linked from main nav
- `images/`, `pdfs/` — unchanged
- `cv.pdf` — linked from About page

---

## Navigation Component

Consistent across all pages. Top bar, 52px height.

```
[ delavega.ai ]   Home  Services  Blog  Publications  About   [ ◐ light ]
```

- **Logo:** left, JetBrains Mono 400, `--accent` color, links to `index.html`
- **Links:** Lexend 300, uppercase, `letter-spacing: 0.08em`, `--text-2` color
- **Active state:** `--accent` color. Each page has one `<a class="nav-active">` on the correct link — hardcoded per page, no JS path-matching needed.
- **Theme toggle:** far right, JetBrains Mono, `--text-3`, bordered pill. JS toggles `data-theme` on `<html>` and saves to `localStorage`.
- **Mobile (`<600px`):** hamburger button (☰) replaces inline links. Clicking opens a full-width dropdown nav below the bar (not a sidebar). Close on link click or outside tap. Implemented with a `nav-open` class on `<nav>` + CSS `max-height` transition.

---

## Page Layouts

### `index.html` — Homepage

**Hero (two-column, `>900px`; single-column stacked below)**

Left column:
- Mono tag: `ml-consulting / cheminformatics` (JetBrains Mono, `--ice`)
- Name: `Antonio de la Vega de León` (Lexend 200, uppercase, `--text-2`)
- h1: `Machine Learning for Drug Discovery` (Lexend 300, ~2.4rem, `--text`)
- Body paragraph: pitch (Lexend 300, `--text-2`)
- Two CTAs: `Get in touch` (filled, `--accent`) + `View services →` (outlined)

Right column (desktop only, left-bordered):
- Three credential stats separated by `<hr>` rules: `26+` publications · `10+` years in cheminformatics · `PhD` ML & computational chemistry
- Stat value: JetBrains Mono 300, large, `--accent`
- Stat label: Lexend 300, small, `--text-3`

**Section cards (3-column grid, 1px border separators)**
Each card: mono tag (JetBrains Mono, `--ice`, uppercase) → card title (Lexend 300) → description (Lexend 300, `--text-3`) → link (JetBrains Mono, `--accent-2`).
Tags: `services` / `writing` / `background`. No numbers.

**Latest post strip**
Single horizontal bar: `latest post` label (mono, `--text-3`) · post title + date/tag (Lexend) · `Read post ›` (mono, `--accent`).
**Data source:** hardcoded HTML, updated manually when a new post is published.

**Footer**
`--bg-alt` background. Logo left (mono, `--text-3`). Social links right: ResearchGate, LinkedIn, ORCID, Mastodon.

---

### `services.html` — Services

**Header section**
- Mono tag: `consulting`
- h1: `Services` (Lexend 300)
- Intro paragraph: what the consultant offers and who the ideal client is

**Service cards (vertical list or 2-column grid)**
Each service entry:
- Service name (Lexend 300, `--text`, ~1.1rem)
- Mono category tag (JetBrains Mono, `--ice`)
- Description paragraph (Lexend 300, `--text-2`)

Suggested services (placeholder — user to fill):
1. QSAR modelling & molecular property prediction
2. ML model interpretation & explainability
3. Cheminformatics pipeline development
4. Scientific consulting / advisory
5. Teaching ML and cheminformatics

**Contact CTA strip** at bottom: "Let's work together" heading + email link or contact button.

---

### `blog.html` — Blog Index

**Header:** mono tag `writing` + h1 `Blog`

**Post list (newest first)**
Each entry (horizontal bar with bottom border):
- Date: JetBrains Mono, `--text-3`, `YYYY-MM-DD`
- Title: Lexend 300, `--text`, links to post
- Topic tag: JetBrains Mono, `--ice`, uppercase
- Read time estimate: mono, `--text-3`

---

### `about.html` — About

Content migrated from: `index.html` (bio), `research.html`, `outreach.html`, `teaching.html`.

**Sections (vertical, separated by `<hr>`):**
1. **Bio** — portrait image (`images/portrait_small.jpg`, floated right on desktop), name, professional intro paragraphs, social links
2. **Research interests** — brief section from `research.html`
3. **Outreach** — from `outreach.html`
4. **Teaching** — from `teaching.html`
5. **CV** — link to `cv.pdf` as a mono-styled button

---

### `publications.html` — Publications

Existing content kept. Markup rewritten to use new CSS classes. Structure:
- Section headers (Lexend 300, `--accent`)
- Publication entries: authors, title (Lexend), journal (mono, `--text-3`), year, DOI link
- Sub-sections: Peer-reviewed articles, Book chapters, Talks, Posters, Datasets, Blog posts

---

### `posts/*.html` — Blog Posts

Each post shares the global nav/footer. Post-specific markup:
- Post header: date (mono) + h1 title (Lexend 300, large) + tag(s) (mono, `--ice`)
- Body: Lexend 300, `--text`, `line-height: 1.75`
- Code blocks: JetBrains Mono, `--bg-alt` background
- Images: full-width within content column, captions in mono `--text-3`
- Footer nav: `← Back to blog` link (mono)

---

## Shared CSS Architecture (`css/style.css`)

Sections in order:
1. Theme script note + `@import` for Google Fonts (or `<link>` in HTML — prefer `<link>`)
2. CSS custom properties (`:root` light, `[data-theme="dark"]` dark, `@media` dark fallback)
3. Reset / base (`box-sizing`, `margin: 0`, `font-family`)
4. Typography scale (h1–h4, body, `.mono-tag`, `.subtitle`)
5. Layout utilities (`.container`, max-width 860px, centered)
6. Navigation component
7. Hero component
8. Section cards
9. Blog index list
10. Blog post layout
11. Publications list
12. About page layout
13. Services page layout
14. Footer
15. Responsive breakpoints (900px, 600px)
16. Animation keyframes + stagger classes
17. `@media (prefers-reduced-motion: reduce)` overrides

---

## Verification Checklist

1. Open `index.html` — hero, cards, post strip, footer all render in light mode
2. Click ◐ toggle — switches to dark mode, persists on page reload
3. Simulate OS dark mode preference (`prefers-color-scheme: dark`) — auto-activates dark before JS
4. Navigate to each page — active nav link highlighted, fonts/colors consistent
5. Open `posts/2025_10_09_fp_comparison.html` — new nav/footer, new styles applied
6. Open `blog.html` — post list shows existing post
7. Open `about.html` — bio, portrait, research/outreach/teaching sections present
8. Resize to 375px — hamburger appears, opens dropdown, layout stacks cleanly
9. Check WCAG contrast on dark mode body text: `--text-2` (`#7a9cb8`) on `--bg` (`#0e1926`) ≥ 4.5:1
10. Confirm `research.html`, `outreach.html`, `teaching.html` are removed; no broken links remain
