# Tokens — Color, Typography, Spacing, Motion

Full token reference for both dark (default) and light modes. Read this file whenever generating any visual output.

**Quick map**: §1 Color (surfaces, text, accents, triple-opacity, chart palettes, grid texture) (L9–241) · §2 Typography (font stack, sizes, page header) (L242–353) · §3 Spacing/Radius/Z-index/Breakpoints/Containers (L354–437) · §4 Motion (durations, entry animation, reduced-motion) (L438–495) · §5 Theme switching (HTML setup, toggle, system pref, print) (L496–556) · §6 Accessibility (WCAG ratios, focus, touch targets) (L557–590)

---

## 1. Color tokens

### 1.1 Surfaces — 3-tier depth

Stack: `--bg → --surface → --card`. No `box-shadow` for chrome elevation.

```css
:root {
  /* DARK (default) */
  --bg:       #0a0a0f;  /* page background — darkest */
  --surface:  #111118;  /* sticky bars, table headers, code blocks */
  --card:     #16161f;  /* elevated cards, modals, popovers */
  --border:   #2a2a3a;  /* hairline 1px borders */
  --hairline: rgba(255,255,255,0.04); /* even subtler dividers */

  /* RGB channel triplets — for use with rgb(... / α) syntax in
     translucent overlays, blurred sticky bars, modal backdrops.
     Without these, you cannot make a theme-adaptive backdrop-blur navbar. */
  --bg-rgb:      10 10 15;
  --surface-rgb: 17 17 24;
  --card-rgb:    22 22 31;
  --border-rgb:  42 42 58;
}

[data-theme="light"] {
  --bg:       #fafafa;  /* page background — lightest gray, not pure white */
  --surface:  #f0f0f4;  /* sticky bars, table headers, code blocks */
  --card:     #ffffff;  /* elevated cards (whitest = top of stack) */
  --border:   #d4d4dc;  /* hairline 1px borders */
  --hairline: rgba(0,0,0,0.05);

  --bg-rgb:      250 250 250;
  --surface-rgb: 240 240 244;
  --card-rgb:    255 255 255;
  --border-rgb:  212 212 220;
}
```

**Usage**: `background: rgb(var(--bg-rgb) / 0.85)` for translucent navbars, `rgba` is the legacy syntax — always prefer the modern slash form to keep theme-swap working.

**Note on light mode inversion**: in dark mode, *deeper* = lower stack. In light mode, *whiter* = higher stack. The semantic intent is preserved: `--card` is always "most elevated".

### 1.2 Text tokens — WCAG-verified

```css
:root {
  /* DARK — verified on #0a0a0f */
  --text-primary:   #f0f0f8;  /* 17.4:1 ✓ AAA — body, headings */
  --text-secondary: #a8a8c0;  /*  8.5:1 ✓ AAA — secondary copy */
  --text-muted:     #8888a8;  /*  5.8:1 ✓ AA  — captions, labels */
  --text-dim:       #5a5a78;  /*  3.0:1 ✓ AA-large only — decorative */
}

[data-theme="light"] {
  /* LIGHT — verified on #fafafa */
  --text-primary:   #0a0a0f;  /* 19.5:1 ✓ AAA — body, headings */
  --text-secondary: #3a3a4a;  /* 11.0:1 ✓ AAA — secondary copy */
  --text-muted:     #6a6a7e;  /*  5.6:1 ✓ AA  — captions, labels */
  --text-dim:       #9a9aae;  /*  2.8:1 ⚠ AA-large only — decorative */
}
```

**Critical**: never use `--text-dim` for normal-size body. It only passes WCAG AA for ≥18pt or ≥14pt-bold ("large text" tier) in either theme.

### 1.3 Accent colors — semantic + identity

Each accent is declared with **both** the hex value AND a space-separated RGB channel triplet. The triplet enables the triple-opacity rule via `rgb()` syntax across both themes.

```css
:root {
  /* DARK — vibrant accents, optimized for dark bg */
  --accent-green:      #00e5a0;
  --accent-green-rgb:  0 229 160;
  --accent-blue:       #3b82f6;
  --accent-blue-rgb:   59 130 246;
  --accent-yellow:     #f5d000;
  --accent-yellow-rgb: 245 208 0;
  --accent-red:        #ff4560;
  --accent-red-rgb:    255 69 96;
  --accent-purple:     #c084fc;
  --accent-purple-rgb: 192 132 252;
  --accent-cyan:       #22d3ee;
  --accent-cyan-rgb:   34 211 238;
}

[data-theme="light"] {
  /* LIGHT — darkened to maintain ≥4.5:1 contrast on #fafafa */
  --accent-green:      #008060;  /* 4.7:1 ✓ AA */
  --accent-green-rgb:  0 128 96;
  --accent-blue:       #1d4ed8;  /* 7.5:1 ✓ AAA */
  --accent-blue-rgb:   29 78 216;
  --accent-yellow:     #936a00;  /* 4.6:1 ✓ AA — yellow is hardest on light */
  --accent-yellow-rgb: 147 106 0;
  --accent-red:        #c41e3a;  /* 5.6:1 ✓ AA */
  --accent-red-rgb:    196 30 58;
  --accent-purple:     #7c3aed;  /* 6.4:1 ✓ AAA */
  --accent-purple-rgb: 124 58 237;
  --accent-cyan:       #0e7490;  /* 5.6:1 ✓ AA */
  --accent-cyan-rgb:   14 116 144;
}
```

**Why two values per accent**: the dark-mode hex would fail WCAG when used as foreground text in light mode (e.g. `#00e5a0` on white = 1.5:1, unreadable). Auto-swapping via theme selector keeps text accessible without component code changes.

**Identity accent** — the page's single "hero" hue, declared as its own pair so a subject theme can re-point it without touching anything else:

```css
:root {
  /* IDENTITY ACCENT — defaults to green, so every existing page is unchanged.
     A subject theme (foundations/palettes.md) overrides just these two values
     via [data-accent="…"]; every identity surface (eyebrow, title keyword,
     focus ring, links, TOC active, progress bar) reads them, so one override
     re-skins the whole page at once. This is identity ONLY — the six semantic
     accents above keep their meaning (green=success, red=danger, …) so charts
     and status chrome never lie. */
  --accent-identity:     var(--accent-green);
  --accent-identity-rgb: var(--accent-green-rgb);
}
```

→ Subject palettes that override these (Toán/Lý/Hoá/Xác suất/Tự nhiên/Mật mã) + the rule on what stays semantic: `foundations/palettes.md`.

### 1.4 Triple-opacity rule (the most important pattern)

Every accent is used at exactly three opacity levels for **chip/badge/pill chrome** — never improvise a fourth in that context:

```css
/* SOLID — text, icons, focus rings, single-pixel emphasis */
color: var(--accent-green);

/* SUBTLE BG (0.08–0.10) — pill/badge/tag fill, hover surfaces */
background: rgb(var(--accent-green-rgb) / 0.08);

/* SUBTLE BORDER (0.20–0.25) — pill/badge/tag border, focus outlines */
border: 1px solid rgb(var(--accent-green-rgb) / 0.22);
```

This rule alone gives the design 80% of its visual coherence. Apply to every accent.

**Sanctioned exceptions** — these use cases need their own opacity ranges and do NOT count as breaking the rule:

| Use case | Allowed range | Where it appears |
|---|---|---|
| Focus ring (`box-shadow`) | 0.15 – 0.30 | Input focus, button focus |
| Modal backdrop overlay | 0.60 – 0.85 | `.modal-overlay` background |
| Section/card highlight bg (left-border accent) | 0.05 – 0.07 | `.theorem`, `.definition`, `.warning` boxes |
| Animation flashes (transient) | up to 1.0 | success-pulse, error-shake |

The constraint is on **chip-style chrome** specifically: that's where consistency matters most because dozens of pills/badges sit next to each other on a page.

**Modern syntax note**: `rgb(R G B / A)` (slash syntax, space-separated) is supported in all modern browsers (Chrome 65+, Safari 12.1+, Firefox 113+). For older browsers, the legacy `rgba(R, G, B, A)` form still works — but you'll need hardcoded RGB values, which breaks theme-swap.

### 1.5 Chart palettes — categorical, sequential, diverging

For data viz this is non-negotiable. Without these you'll keep reaching for the same 4 accents and your charts will look broken.

```css
:root {
  /* CATEGORICAL — 10 distinct hues, ordered by perceptual distance (dark mode) */
  --cat-1:  #00e5a0;  /* green */
  --cat-2:  #3b82f6;  /* blue */
  --cat-3:  #f5d000;  /* yellow */
  --cat-4:  #ff4560;  /* red */
  --cat-5:  #c084fc;  /* purple */
  --cat-6:  #fb923c;  /* orange */
  --cat-7:  #22d3ee;  /* cyan */
  --cat-8:  #f472b6;  /* pink */
  --cat-9:  #a3e635;  /* lime */
  --cat-10: #94a3b8;  /* slate (use last — neutral fallback) */

  /* SEQUENTIAL — single-hue gradient for heatmaps, choropleth, density */
  --seq-1: #1a1a2e;  /* darkest — low values */
  --seq-2: #2d3561;
  --seq-3: #3a5fcd;
  --seq-4: #00b4d8;
  --seq-5: #00e5a0;
  --seq-6: #f5d000;  /* lightest — high values */

  /* DIVERGING — for residuals, correlation matrices, +/- deltas */
  --div-neg-3: #ff4560;
  --div-neg-2: #ff7a8b;
  --div-neg-1: #ffaab4;
  --div-zero:  #2a2a3a;  /* matches --border so "no signal" reads as background */
  --div-pos-1: #7eebcc;
  --div-pos-2: #3eddb0;
  --div-pos-3: #00e5a0;
}

[data-theme="light"] {
  /* CATEGORICAL — darkened for light bg */
  --cat-1:  #008060;
  --cat-2:  #1d4ed8;
  --cat-3:  #936a00;
  --cat-4:  #c41e3a;
  --cat-5:  #7c3aed;
  --cat-6:  #c2410c;
  --cat-7:  #0e7490;
  --cat-8:  #be185d;
  --cat-9:  #4d7c0f;
  --cat-10: #475569;

  /* SEQUENTIAL — light mode: light = low, dark = high (perceptually correct) */
  --seq-1: #f0f9ff;
  --seq-2: #bae6fd;
  --seq-3: #38bdf8;
  --seq-4: #0284c7;
  --seq-5: #075985;
  --seq-6: #0c4a6e;

  /* DIVERGING — light mode */
  --div-neg-3: #c41e3a;
  --div-neg-2: #f87171;
  --div-neg-1: #fecaca;
  --div-zero:  #d4d4dc;  /* matches --border in light mode */
  --div-pos-1: #bbf7d0;
  --div-pos-2: #4ade80;
  --div-pos-3: #008060;
}
```

**Usage rules**:
- Categorical: cycle in declaration order. If you need >10 series, the chart needs splitting/faceting — no palette extension will save it.
- Sequential: in dark mode, dark=low; in light mode, light=low. Both are perceptually correct (low values feel "absent"). If interpolating intermediate stops, use OKLCH/Lab space — never naively in RGB.
- Diverging: zero-point exactly matches `--border` so "no change" reads as background.

### 1.6 Background grid texture (signature element)

```css
body::before {
  content: '';
  position: fixed;
  inset: 0;
  background-image:
    repeating-linear-gradient(0deg,  transparent 0 39px, var(--hairline) 39px 40px),
    repeating-linear-gradient(90deg, transparent 0 39px, var(--hairline) 39px 40px);
  z-index: 0;
  pointer-events: none;
}
body > * { position: relative; z-index: 1; }

/* Disable inside data-viz containers — competes with chart gridlines */
.chart-container, .plot-container, .canvas-container {
  isolation: isolate;
  background: var(--card);
}
```

The grid uses `--hairline` (auto-swaps with theme), so it's subtle in both modes.

→ Alternative & animated background textures (dots, crosshatch, drift, wave, contour, scanline, glow): `foundations/backgrounds.md`. They all reuse this `body::before` scaffold, so the chart-container exclusion above and the print rule in §5.4 cover them automatically — no duplication.

---

## 2. Typography

### 2.1 Font stack

```css
:root {
  --font-display: 'Bebas Neue', 'Oswald', 'Arial Narrow', sans-serif;
  --font-body:    'DM Sans', 'Söhne', 'Helvetica Neue', system-ui, sans-serif;
  --font-mono:    'JetBrains Mono', 'Fira Code', 'SF Mono', Menlo, monospace;
  --font-math:    'KaTeX_Main', 'Latin Modern Math', 'Cambria Math', serif;
}
```

**Why these**: Bebas Neue is unmistakable, free, and renders well at large sizes. DM Sans has excellent tabular numerals across 5 weights. JetBrains Mono has best-in-class ligatures.

**Single Google Fonts request**:
```html
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;700&family=JetBrains+Mono:wght@400;500&display=swap">
```

### 2.2 Type scale (responsive, fluid)

```css
:root {
  /* Display — Bebas Neue, line-height 0.9 */
  --fs-hero: clamp(40px, 8vw, 100px);   /* page hero only */
  --fs-h1:   clamp(32px, 5vw, 56px);
  --fs-h2:   clamp(24px, 3.5vw, 36px);
  --fs-h3:   20px;

  /* Body — DM Sans, line-height 1.5 */
  --fs-lg:   18px;  /* lead paragraphs */
  --fs-base: 14px;  /* default body */
  --fs-sm:   13px;  /* secondary body */
  --fs-xs:   12px;  /* captions */

  /* Mono — JetBrains Mono, line-height 1.6 */
  --fs-code:  13px;
  --fs-label: 11px;  /* eyebrow labels */
  --fs-micro: 10px;  /* badge content, metadata */
  --fs-nano:   9px;  /* inline tags */
}
```

### 2.3 Tabular numerals (essential for data)

```css
.numeric, table td, .stat, .metric, code, pre,
input[type="number"], .tabular {
  font-variant-numeric: tabular-nums lining-nums;
  font-feature-settings: 'tnum' 1, 'lnum' 1;
}
```

Without this, "1,234,567" and "9,876,543" render at different widths and tables look amateurish.

### 2.4 Page header — signature composition

The eyebrow → giant-title → small-subtitle pattern. Use this for every page/section header.

```html
<header class="page-head">
  <span class="eyebrow">SECTION 03 · CHAPTER 04</span>
  <h1 class="title">Gradient <span>Descent</span></h1>
  <p class="subtitle">An iterative optimization algorithm for finding the local minimum of a differentiable function.</p>
  <div class="meta">UPDATED <b>2026-05-04</b> · DIFFICULTY <b>INTERMEDIATE</b> · 12 MIN READ</div>
</header>
```

```css
.page-head { display: flex; flex-direction: column; gap: var(--sp-3); }

.eyebrow {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.2em;
  color: var(--accent-identity);
  text-transform: uppercase;
}

.title {
  font-family: var(--font-display);
  font-size: var(--fs-hero);
  line-height: 0.9;
  color: var(--text-primary);
  letter-spacing: 0.01em;
}
.title span { color: var(--accent-identity); }  /* exactly ONE keyword highlighted */

.subtitle {
  font-family: var(--font-body);
  font-weight: 300;
  font-size: var(--fs-base);
  color: var(--text-secondary);
  max-width: 520px;
  line-height: 1.5;
}

.meta {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  color: var(--text-dim);
  letter-spacing: 0.1em;
}
.meta b { color: var(--accent-yellow); font-weight: normal; }
```

**Rule**: in `.title`, only ONE keyword gets the accent color. Highlighting more dilutes the emphasis.

---

## 3. Spacing, Radius, Layout

### 3.1 Spacing scale (4px base)

```css
:root {
  --sp-0:   0;
  --sp-1:   4px;
  --sp-2:   8px;
  --sp-3:  12px;
  --sp-4:  16px;
  --sp-5:  20px;
  --sp-6:  24px;
  --sp-8:  32px;
  --sp-10: 40px;
  --sp-12: 48px;
  --sp-16: 64px;
  --sp-20: 80px;
  --sp-24: 96px;
  --sp-32: 128px;
}
```

**Component padding conventions**:
- Pill/badge: `--sp-1 --sp-2`
- Input/button: `--sp-3 --sp-4` (height ≥ 36px desktop, ≥ 44px mobile)
- Card: `--sp-5 --sp-6`
- Section: `--sp-12` to `--sp-20` between blocks
- Page gutter: `clamp(--sp-4, 4vw, --sp-12)`

### 3.2 Radius scale

```css
:root {
  --r-xs:    3px;   /* inline tags */
  --r-sm:    6px;   /* buttons, inputs */
  --r-md:   10px;   /* cards, note boxes */
  --r-lg:   12px;   /* table wrappers, modals */
  --r-xl:   16px;   /* hero cards */
  --r-pill: 9999px; /* pills, switches */
}
```

### 3.3 Z-index scale

```css
:root {
  --z-base:     1;
  --z-sticky:  10;
  --z-dropdown: 100;
  --z-overlay: 1000;
  --z-modal:   1100;
  --z-toast:   1200;
  --z-debug:   9999;
}
```

### 3.4 Breakpoints (mobile-first)

```css
:root {
  --bp-sm:   640px;  /* large phone landscape, small tablet */
  --bp-md:   768px;  /* tablet portrait */
  --bp-lg:  1024px;  /* tablet landscape, small laptop */
  --bp-xl:  1280px;  /* desktop */
  --bp-2xl: 1536px;  /* large desktop */
}
```

### 3.5 Container widths

```css
.container { width: 100%; padding-inline: var(--sp-4); margin-inline: auto; }
@media (min-width: 640px)  { .container { max-width: 640px; } }
@media (min-width: 768px)  { .container { max-width: 768px; padding-inline: var(--sp-6); } }
@media (min-width: 1024px) { .container { max-width: 1024px; } }
@media (min-width: 1280px) { .container { max-width: 1200px; padding-inline: var(--sp-12); } }

/* Reading-optimized — for long-form articles, math, theorems */
.prose-container { max-width: 68ch; }
```

---

## 4. Motion

### 4.1 Tokens

```css
:root {
  --dur-instant: 80ms;   /* micro-states (button press) */
  --dur-fast:   150ms;   /* hover, focus */
  --dur-base:   250ms;   /* default transitions */
  --dur-slow:   400ms;   /* modals, drawers */
  --dur-entry:  650ms;   /* page-load reveals */

  --ease-out:    cubic-bezier(0.16, 1, 0.3, 1);     /* default — arrives, settles */
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);      /* symmetric */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1); /* slight overshoot — playful CTA */
}
```

### 4.2 Entry animation (signature)

```css
@keyframes enter {
  from { opacity: 0; transform: translateY(16px); }
  to   { opacity: 1; transform: translateY(0); }
}

.reveal {
  opacity: 0;
  animation: enter var(--dur-entry) var(--ease-out) forwards;
}

/* Stagger via nth-child — cap at 600ms total */
.reveal:nth-child(1) { animation-delay: 0ms; }
.reveal:nth-child(2) { animation-delay: 80ms; }
.reveal:nth-child(3) { animation-delay: 160ms; }
.reveal:nth-child(4) { animation-delay: 240ms; }
.reveal:nth-child(5) { animation-delay: 320ms; }
.reveal:nth-child(6) { animation-delay: 400ms; }
.reveal:nth-child(n+7) { animation-delay: 480ms; }  /* cap */
```

For lists with many items, prefer **IntersectionObserver** triggering `.is-visible` over CSS `animation-delay` — otherwise late items animate while user is already scrolled past.

### 4.3 Reduced motion (mandatory)

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## 5. Theme switching

### 5.1 HTML setup

Default to dark; let user opt into light.

```html
<!DOCTYPE html>
<html lang="en" data-theme="dark">
  <head>...</head>
  <body>...</body>
</html>
```

### 5.2 Toggle button + localStorage

```html
<button class="btn btn-ghost" id="theme-toggle" aria-label="Toggle theme">
  <span class="theme-label">DARK</span>
</button>

<script>
  (function() {
    const root = document.documentElement;
    const saved = localStorage.getItem('theme');
    if (saved === 'light') root.setAttribute('data-theme', 'light');

    document.getElementById('theme-toggle').addEventListener('click', () => {
      const next = root.getAttribute('data-theme') === 'light' ? 'dark' : 'light';
      root.setAttribute('data-theme', next);
      localStorage.setItem('theme', next);
      document.querySelector('.theme-label').textContent = next.toUpperCase();
    });
  })();
</script>
```

### 5.3 Respect system preference (optional)

```js
// Use system preference if user hasn't chosen yet
if (!localStorage.getItem('theme') && window.matchMedia('(prefers-color-scheme: light)').matches) {
  document.documentElement.setAttribute('data-theme', 'light');
}
```

### 5.4 Print styles — force light mode for print

```css
@media print {
  :root { /* override to light tokens regardless of data-theme */
    --bg: #ffffff; --surface: #f5f5f5; --card: #ffffff; --border: #cccccc;
    --text-primary: #000; --text-secondary: #333; --text-muted: #555;
  }
  body::before { display: none; }  /* hide grid texture */
  .reveal { opacity: 1 !important; animation: none !important; }
}
```

---

## 6. Accessibility — WCAG ratios

| Token             | DARK on `--bg` (#0a0a0f) | LIGHT on `--bg` (#fafafa) | Use for                   |
| ----------------- | ------------------------ | ------------------------- | ------------------------- |
| `--text-primary`  | 17.4 : 1 ✓ AAA           | 19.5 : 1 ✓ AAA            | body, headings            |
| `--text-secondary`| 8.5 : 1 ✓ AAA            | 11.0 : 1 ✓ AAA            | secondary copy            |
| `--text-muted`    | 5.8 : 1 ✓ AA             | 5.6 : 1 ✓ AA              | captions, labels          |
| `--text-dim`      | 3.0 : 1 ⚠ AA-large only  | 2.8 : 1 ⚠ AA-large only   | decorative, ≥18pt or ≥14pt-bold |
| `--accent-green`  | 12.0 : 1 ✓ AAA           | 4.7 : 1 ✓ AA              | text on bg               |
| `--accent-blue`   | 5.4 : 1 ✓ AA             | 7.5 : 1 ✓ AAA             | text on bg               |
| `--accent-yellow` | 13.1 : 1 ✓ AAA           | 4.6 : 1 ✓ AA              | text on bg               |
| `--accent-red`    | 5.9 : 1 ✓ AA             | 5.6 : 1 ✓ AA              | text on bg               |
| `--accent-purple` | 7.4 : 1 ✓ AAA            | 6.4 : 1 ✓ AAA             | text on bg               |
| `--accent-cyan`   | 9.8 : 1 ✓ AAA            | 5.6 : 1 ✓ AA              | text on bg               |

**Never** paint accent-colored text on `--card` or `--surface` without re-checking. The 0.06–0.10 alpha tints are NOT for foreground text.

### Focus indicators

```css
:focus-visible {
  outline: 2px solid var(--accent-identity);
  outline-offset: 2px;
  border-radius: var(--r-xs);
}
```

Never remove with `outline: none` unless replaced with a `:focus-visible` ring.

### Touch targets

- Mobile: minimum 44 × 44 pt (Apple HIG, also Material 48dp / WCAG 2.5.5 AAA)
- Desktop: 32 × 32 px acceptable for dense UIs (data tables); ≥40 × 40 for primary actions
