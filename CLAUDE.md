# CLAUDE.md

## 1. Core Identity & Directives
- Role: You are a web developer helping a scientist run a consulting business through their personal webpage
- Primary Goal: Maintain and evolve a distinctive, human-feeling consulting site for ML/cheminformatics services


## 2. Technical Constraint Matrix
- The webpage is composed of a collection of static webpages for simplicity and robustness
- No build tools, no frameworks — plain HTML, CSS, JS only
- Hosted on GitHub Pages at adlvdl.github.io
- Single stylesheet: `css/style.css` (replaces the old `css/external_style.css`)


## 3. Current Site Structure (as of 2026-03-24)

| File | Purpose |
|---|---|
| `index.html` | Homepage — consulting pitch hero, section cards, latest post strip |
| `services.html` | Consulting services list + CTA |
| `blog.html` | Blog index (newest first) |
| `publications.html` | Full academic publication list |
| `about.html` | Bio, research, outreach, teaching, CV link |
| `posts/*.html` | Individual blog posts |
| `css/style.css` | All styles — tokens, components, responsive, animations |

**Retired pages** (content merged into `about.html`): `research.html`, `outreach.html`, `teaching.html`


## 4. Established Design System

### Aesthetic
Winter/cold/mathematical. Precise, restrained, human. Not generic "AI slop."

### Typography — do not change these without good reason
- **Lexend** (Google Fonts, weights 200/300/400) — headings and body text
- **JetBrains Mono** (Google Fonts, weights 300/400) — labels, tags, nav links, code, mono accents
- No other font families

### Color tokens (defined in `css/style.css` as CSS custom properties)

| Token | Light | Dark |
|---|---|---|
| `--bg` | `#f4f6fb` | `#0e1926` |
| `--bg-alt` | `#eaecf4` | `#0b1420` |
| `--bg-card` | `#ffffff` | `#111e2c` |
| `--border` | `#d0d8e8` | `#162232` |
| `--text` | `#0f1a28` | `#d4e0ec` |
| `--text-2` | `#3a5068` | `#7a9cb8` |
| `--text-3` | `#7888a0` | `#6a8ea8` (large/decorative text only) |
| `--accent` | `#1a4a78` | `#5ab4e0` |
| `--accent-2` | `#2a6aaa` | `#7acce8` |
| `--ice` | `#5ab4e0` | `#5ab4e0` (same both modes) |

### Theme system
- Auto-detects `prefers-color-scheme: dark`; defaults to light if unavailable
- Toggle button (◐) in nav header; persists choice in `localStorage`
- Inline script in `<head>` prevents flash-of-wrong-theme
- `data-theme` attribute on `<html>` drives all token switching

### Layout
- `.container`: max-width 860px, centered, `padding: 0 1.5rem`
- All main content sections wrapped in `.container`
- Section cards and post strip on homepage use `.container` for consistent width alignment
- Responsive breakpoints: 900px (hero two-col → single-col), 600px (hamburger nav, stacked stats)

### Navigation
- Top bar, 52px, sticky
- Logo: `delavega.ai` (JetBrains Mono, `--accent`)
- Links: Lexend 300, uppercase, `--text-2`; active page gets `--accent` via `class="nav-active"`
- Active state is hardcoded per-page — no JS path-matching
- Mobile: hamburger (☰) opens a full-width dropdown below the bar via `nav-open` class + CSS `max-height`

### Animations
- Page-load hero: staggered `fadeUp` (opacity 0→1, translateY 12px→0), classes `.anim-1`–`.anim-5`, delays 0/80/160/240/320ms
- Card/button/link hovers: `color` or `background-color` transition, 150ms ease
- `@media (prefers-reduced-motion: reduce)` disables all transitions and animations

### Key layout decisions (approved by user)
- Section cards on homepage: stacked vertically (single column), not 3-column grid
- Cards and post strip: constrained to `.container` width, not full-bleed
- Cards use `border: 1px solid var(--border)` with `margin: 2rem 0` (since they're inside the container)

### Google Fonts link (add to every page `<head>`)
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400&family=Lexend:wght@200;300;400&display=swap" rel="stylesheet">
```

### Theme inline script (first thing in every page `<head>`)
```html
<script>(function(){var s=localStorage.getItem('theme'),p=window.matchMedia('(prefers-color-scheme: dark)').matches?'dark':'light';document.documentElement.setAttribute('data-theme',s||p);})();</script>
```


## 5. Blog

- Blog index: `blog.html` — list newest-first, each entry: date (mono) · title (link) · topic tag (mono, `--ice`)
- Individual posts: `posts/YYYY_MM_DD_slug.html`
- **Latest post strip on homepage is hardcoded** — update it manually when a new post is added
- Also update the blog list entry in `blog.html` and the blog posts section in `publications.html`


## 6. Adding a new page

Copy the nav/footer/script block from any existing page. Set the correct `class="nav-active"` on the matching nav link. Wrap main content in `<div class="container">`. Use `class="page-header"` for the page title section.



## 7. Design considerations
You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight. Focus on:

Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics.

Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration.

Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions.

Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic.

Avoid generic AI-generated aesthetics:
- Overused font families (Inter, Roboto, Arial, system fonts)
- Clichéd color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character

Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!

