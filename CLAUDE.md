# árido — Landing Page Tech Spec

## Project Overview

Static landing page for **árido**, a software factory. Single-file delivery, zero dependencies, Lighthouse 100 target.

---

## Constraints & Non-Negotiables

- **Pure HTML + CSS + JS only** — no frameworks, no libraries, no bundlers
- **Single `index.html` file** — all CSS and JS inlined via `<style>` and `<script>` tags
- **No external requests at runtime** — no CDN fonts, no analytics scripts, no third-party anything
- **Total page weight < 150KB** (HTML + inlined assets, excluding logo PNGs)
- **Logo PNGs referenced as relative paths** from `static/logos/`
- **No CSS resets from CDN** — write only what's needed, from scratch
- **JS is progressive enhancement only** — the page must be fully readable with JS disabled

---

## Performance Requirements

| Metric | Target |
|---|---|
| Lighthouse Performance | 95+ |
| Lighthouse Accessibility | 100 |
| Lighthouse Best Practices | 100 |
| Lighthouse SEO | 100 |
| First Contentful Paint | < 0.8s |
| Largest Contentful Paint | < 1.5s |
| Cumulative Layout Shift | 0 |
| Time to Interactive | < 1s |

### Techniques to apply

- **Critical CSS inlined** in `<head>` — no render-blocking stylesheets
- **System font stack** — no web font downloads: `'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- **`width` + `height` attributes on all `<img>` tags** — prevents layout shift
- **`loading="lazy"` on below-fold images only** — hero logo loads eagerly
- **`fetchpriority="high"` on hero logo**
- **No scroll-triggered animations via JS** — use `@media (prefers-reduced-motion)` aware CSS `animation-timeline: scroll()` or `IntersectionObserver` with minimal JS
- **`will-change` only where GPU compositing is intentional** — not as a blanket rule
- **Minify all HTML, CSS, JS** in the final file (no dev comments in production)
- **`<link rel="preconnect">` is forbidden** since there are no external resources
- **`defer` any JS** — nothing blocks parsing

---

## Color Palette

Official brand palette. All values as CSS custom properties on `:root`.

| Swatch | HEX | RGB | Role |
|---|---|---|---|
| Rust orange | `#BC3908` | 188, 57, 8 | Primary brand / CTA |
| Forest green | `#3A5A40` | 58, 90, 64 | Secondary accent |
| Yellow | `#F4E409` | 244, 228, 9 | Highlight / attention |
| Black | `#000000` | 0, 0, 0 | Dark surfaces / text |
| Light gray | `#F2F2F2` | 242, 242, 242 | Light backgrounds |

```css
:root {
  /* Brand primaries */
  --c-brand:        #BC3908;  /* rust orange — primary CTA, accents */
  --c-brand-dark:   #972D06;  /* hover / pressed — darkened ~15% */
  --c-brand-light:  #E04610;  /* highlight — lightened ~15% */

  --c-secondary:    #3A5A40;  /* forest green — secondary accent */
  --c-secondary-dk: #2A4230;  /* hover on green elements */

  --c-yellow:       #F4E409;  /* yellow — use sparingly, high-attention only */

  /* Surfaces */
  --c-bg:           #F2F2F2;  /* light gray — main background */
  --c-bg-dark:      #000000;  /* black — dark sections (hero, footer) */
  --c-bg-card:      #FFFFFF;  /* white — card surfaces */
  --c-border:       #D8D8D8;  /* slightly darker gray — dividers */

  /* Typography */
  --c-text:         #000000;  /* body text on light backgrounds */
  --c-text-muted:   #555555;  /* secondary / caption text */
  --c-text-inverse: #F2F2F2;  /* text on dark backgrounds */
}
```

### Color usage guidelines

- `--c-brand` (#BC3908): primary buttons, active nav indicator, section accent lines, icon fills
- `--c-secondary` (#3A5A40): secondary buttons, tag labels, "why us" bullet marks
- `--c-yellow` (#F4E409): use **only** for 1–2 high-impact moments (e.g., a single word in the hero headline, a highlight badge) — never as a background for large areas
- `--c-bg-dark` (#000000): hero section, footer, process section — use `Negativo horizontal.png` logo here
- `--c-bg` (#F2F2F2): services section, why section — use `Positivo horizontal.png` logo here
- Contrast check: `#BC3908` on `#F2F2F2` → 4.6:1 ✓ (passes AA for normal text)

### Logo usage rules

| Context | File |
|---|---|
| Light background (default) | `static/logos/Positivo horizontal.png` |
| Dark background sections | `static/logos/Negativo horizontal.png` |
| Square/icon contexts | `static/logos/Positivo.png` |

---

## Page Architecture

### File structure
```
index.html          ← single deliverable
static/
  logos/
    Positivo horizontal.png
    Positivo.png
    Negativo horizontal.png
    Negativo.png
```

### HTML skeleton

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>árido — Software Factory</title>
  <meta name="description" content="...">
  <meta name="theme-color" content="#C03A1C">
  <!-- Open Graph -->
  <meta property="og:title" content="árido — Software Factory">
  <meta property="og:image" content="static/logos/Positivo horizontal.png">
  <style>/* ALL CSS HERE */</style>
</head>
<body>
  <header>...</header>
  <main>
    <section id="hero">...</section>
    <section id="services">...</section>
    <section id="process">...</section>
    <section id="why">...</section>
    <section id="contact">...</section>
  </main>
  <footer>...</footer>
  <script>/* ALL JS HERE */</script>
</body>
</html>
```

---

## Sections Spec

### 1. `<header>` — Sticky nav

- Fixed top, `backdrop-filter: blur(12px)` on scroll (JS adds class `.scrolled`)
- Left: `Positivo horizontal.png` logo, height 32px, `fetchpriority="high"`
- Right: nav links — `Servicios`, `Proceso`, `Contacto` — smooth scroll anchors
- Mobile: hamburger toggle, nav collapses to full-screen overlay
- Background transitions from `transparent` → `rgba(247,244,240,0.92)` on scroll

### 2. `#hero` — First viewport

- `min-height: 100svh`, centered content, dark background (`--c-bg-dark`)
- Logo: `Negativo horizontal.png`, max-width 280px, eager load
- Headline: large, bold, warm white — e.g., *"Construimos software que funciona."*
- Sub-headline: 1–2 sentences, `--c-text-muted` toned down
- CTA button: `--c-brand` background, white text, hover → `--c-brand-dark`
- Subtle background texture: pure CSS radial gradient — no images
- Decorative element: the logo circle mark used as a large faint watermark (CSS `opacity: 0.04`)

### 3. `#services` — What we build

- Light background (`--c-bg`)
- 3–4 service cards in CSS Grid (`repeat(auto-fit, minmax(280px, 1fr))`)
- Each card: icon (inline SVG — no external requests), title, short description
- Cards have `border: 1px solid var(--c-border)`, subtle hover lift (`transform: translateY(-4px)`)
- Suggested services: Desarrollo a medida · Plataformas SaaS · APIs & Integraciones · Consultoría técnica

### 4. `#process` — How we work

- Dark background (`--c-bg-dark`), white text
- Horizontal numbered steps on desktop, vertical on mobile
- Steps connected by a line — pure CSS, no images
- 4 steps: Descubrimiento → Diseño → Desarrollo → Entrega

### 5. `#why` — Why árido

- Light background, 2-column layout (text left, visual right)
- Visual: abstract CSS art using the brand circle motif — no images required
- 3–4 bullet differentiators with brand-colored accent marks

### 6. `#contact` — CTA / Contact

- Dark background, centered
- Simple `<form>` with: name, email, message, submit
- **No backend required at spec time** — form `action` left as `#` with JS `preventDefault` + thank-you swap
- Fields styled with bottom-border-only aesthetic (minimal, modern)
- CTA: primary button in `--c-brand`

### 7. `<footer>`

- Minimal: logo (small), copyright, optional links
- Dark background, same as `--c-bg-dark`

---

## CSS Architecture

All in one `<style>` block. Order:

1. Custom properties (`:root`)
2. Reset (only box-sizing, margin, padding — ~10 rules)
3. Typography scale (using `clamp()` for fluid type)
4. Layout utilities (`.container`, `.grid`, `.flex`)
5. Components (nav, button, card, form)
6. Section-specific overrides
7. Animations (wrapped in `@media (prefers-reduced-motion: no-preference)`)
8. Responsive breakpoints (mobile-first, one breakpoint at `768px`, one at `1200px`)

### Typography scale (fluid)

```css
--text-xs:   clamp(0.75rem,  0.7rem  + 0.25vw, 0.875rem);
--text-sm:   clamp(0.875rem, 0.8rem  + 0.35vw, 1rem);
--text-base: clamp(1rem,     0.9rem  + 0.5vw,  1.125rem);
--text-lg:   clamp(1.125rem, 1rem    + 0.6vw,  1.375rem);
--text-xl:   clamp(1.375rem, 1.2rem  + 0.9vw,  1.75rem);
--text-2xl:  clamp(1.75rem,  1.5rem  + 1.2vw,  2.5rem);
--text-3xl:  clamp(2.5rem,   2rem    + 2.5vw,  4rem);
```

---

## JS Architecture

All in one `<script>` block at end of `<body>`. Vanilla ES2020+, no transpilation.

### Responsibilities (only)

1. **Sticky nav class toggle** — add `.scrolled` on `window.scroll` (passive listener)
2. **Mobile nav toggle** — hamburger open/close
3. **Smooth scroll polyfill guard** — `scroll-behavior: smooth` in CSS is enough; JS only if `matchMedia('(prefers-reduced-motion)')` forces override
4. **Form submit handler** — prevent default, swap form for thank-you message
5. **Fade-in on scroll** — `IntersectionObserver` adds `.visible` class to sections; CSS handles the actual animation

Total JS budget: **< 2KB** unminified.

---

## Accessibility Requirements

- All images have descriptive `alt` text
- Color contrast ratio ≥ 4.5:1 for all text (verify with brand orange on white)
- Focus styles visible and on-brand (`outline: 2px solid var(--c-brand)`)
- Nav landmark (`<nav>`), main landmark (`<main>`), footer landmark (`<footer>`)
- Form `<label>` elements properly associated via `for`/`id`
- Skip-to-content link as first focusable element
- `aria-label` on hamburger button, `aria-expanded` toggled by JS

---

## SEO

- One `<h1>` only (hero headline)
- Section headings are `<h2>`, card headings `<h3>`
- `<meta name="description">` under 160 chars
- Structured data: `Organization` JSON-LD in `<head>`
- `lang="es"` on `<html>`
- Canonical `<link rel="canonical">` pointing to production URL (placeholder until deployed)

---

## Implementation Notes

- **Do not use Tailwind, Bootstrap, or any utility CSS library**
- **Do not use Alpine.js, Vue, React, or any JS framework**
- **Do not import or `require()` anything**
- **Write semantic HTML** — `<section>`, `<article>`, `<nav>`, `<header>`, `<footer>`, `<main>`
- **Avoid `div` soup** — use semantic elements first, `div` only as layout wrappers
- **No `!important`** — specificity must be managed through structure
- **No inline `style` attributes** in HTML — all styling via CSS classes
- The final file should pass **HTML validation** (W3C validator)
- Use `<!-- SECTION: name -->` comments to delimit sections in the source for readability
