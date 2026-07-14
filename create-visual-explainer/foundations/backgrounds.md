# Backgrounds — Texture Variants for the Signature Layer

The fixed `body::before` grid in `foundations/tokens.md` §1.6 is the skill's signature texture — and because it never changes, every page wears the same one. This file holds **drop-in alternatives** for that layer (dots, crosshatch, contour, scanline, drift, wave, glow), selected declaratively, so pages vary without losing the "industrial flat" feel.

Read this when you want a page's background to differ from the default grid — the texture half of a theme set; the color half is `foundations/palettes.md`.

**Quick map**: §1 How selection works (L11–29) · §2 Static variants (L30–68) · §3 Animated variants — drift, wave (L69–104) · §4 Identity-tinted variants (L105–120) · §5 Guardrails + what you get for free (L121–137) · §6 Adding a variant (L138–144)

---

## 1. How selection works

Every variant overrides the **same `body::before` scaffold** that `tokens.md` §1.6 already establishes (`position:fixed; inset:0; z-index:0; pointer-events:none`, plus `body > * { z-index:1 }`). You don't restate the scaffold — you change only the `background-image` (and, for animated variants, add a `transform` animation). Select on the root element, next to `data-accent`:

```html
<html lang="en" data-theme="dark" data-accent="chemistry" data-bg="dots">
```

- omit `data-bg` (or `="grid"`) → the default grid from `tokens.md` §1.6
- `="none"` → no texture at all (clean surface — good for the "polished SaaS" dial, see `SKILL.md` §4)

```css
html[data-bg="none"] body::before { background-image: none; }
```

Because every variant *is* `body::before`, the chart-container exclusion and the print rule the skill already ships apply to all of them automatically (see §5) — no per-variant duplication.

---

## 2. Static variants

All use `--hairline` (auto-swaps dark/light, ~0.04 alpha) so they stay subtle and theme-correct. If a variant reads too faint on a given page, raise its color to an explicit `rgb(... / 0.06)` *on that page* — don't lower the global hairline.

**Dots** — blueprint / graph-paper dots on the 40px grid:
```css
html[data-bg="dots"] body::before {
  background-image: radial-gradient(var(--hairline) 1.2px, transparent 1.3px);
  background-size: 40px 40px;
}
```

**Crosshatch** — 45°/135° weave; more "drafting table" than the square grid:
```css
html[data-bg="crosshatch"] body::before {
  background-image:
    repeating-linear-gradient( 45deg, transparent 0 23px, var(--hairline) 23px 24px),
    repeating-linear-gradient(135deg, transparent 0 23px, var(--hairline) 23px 24px);
}
```

**Contour** — two interfering ring systems read as faint topographic lines; suits `nature`:
```css
html[data-bg="contour"] body::before {
  background-image:
    repeating-radial-gradient(circle at 20% 30%, transparent 0 46px, var(--hairline) 46px 48px),
    repeating-radial-gradient(circle at 80% 70%, transparent 0 60px, var(--hairline) 60px 62px);
}
```

**Scanline** — fine horizontal lines, CRT-terminal feel; suits `crypto`:
```css
html[data-bg="scanline"] body::before {
  background-image: repeating-linear-gradient(0deg, transparent 0 2px, var(--hairline) 2px 3px);
}
```

---

## 3. Animated variants — drift, wave

Two rules govern motion here (both from `SKILL.md`): **animate `transform`/`opacity` only** (rule 9) and **respect `prefers-reduced-motion`**. A continuously drifting layer is the only kind of background motion that satisfies rule 9 cheaply. Each loops on exactly one grid cell so the motion is seamless, and each oversizes the layer (`inset: -…px`) so the translation never exposes an edge.

**Drift** — the default grid sliding slowly on the diagonal:
```css
html[data-bg="drift"] body::before {
  background-image:
    repeating-linear-gradient(0deg,  transparent 0 39px, var(--hairline) 39px 40px),
    repeating-linear-gradient(90deg, transparent 0 39px, var(--hairline) 39px 40px);
  inset: -40px;                                   /* oversize: hide edges during travel */
  animation: bg-drift 24s linear infinite;
  will-change: transform;
}
@keyframes bg-drift { to { transform: translate(40px, 40px); } }   /* one cell -> seamless */
@media (prefers-reduced-motion: reduce) { html[data-bg="drift"] body::before { animation: none; } }
```

**Wave** — two opposed shallow-angle gratings whose moiré shimmers as it drifts; a "gentle field," not literal water:
```css
html[data-bg="wave"] body::before {
  background-image:
    repeating-linear-gradient( 8deg, transparent 0 31px, var(--hairline) 31px 32px),
    repeating-linear-gradient(-8deg, transparent 0 31px, var(--hairline) 31px 32px);
  inset: -32px;
  animation: bg-wave 30s linear infinite;
  will-change: transform;
}
@keyframes bg-wave { to { transform: translateY(32px); } }
@media (prefers-reduced-motion: reduce) { html[data-bg="wave"] body::before { animation: none; } }
```

> Why not a real ripple? A literal domain-warp ripple means animating an SVG `feTurbulence` `baseFrequency` (or a displacement map), which re-rasterizes the filter every frame — expensive, and it animates a filter attribute rather than `transform`/`opacity`, so it breaks rule 9. For a full-page background, don't. If you truly need a ripple, scope it to a small, one-shot, interaction-triggered element, not the page backdrop.

---

## 4. Identity-tinted variants

Most textures stay monochrome (`--hairline`) on purpose. One exception is worth having for "polished SaaS" pages that turn the grid *off*: a single very-faint identity-colored glow, no lines.

**Glow** — gridless radial wash in a corner, tinted by the page's identity accent:
```css
html[data-bg="glow"] body::before {
  background-image:
    radial-gradient(60vw 60vw at 85% -10%, rgb(var(--accent-identity-rgb) / 0.05), transparent 70%);
}
```

`0.05` sits inside the sanctioned "section-highlight" alpha band (`tokens.md` §1.4), and this is a background, not chrome — so it doesn't break the no-gradient-in-chrome rule (rule 6). Keep it ≤ 0.06; above that it stops being ambient and starts competing with content. Because it reads `--accent-identity-rgb`, it recolors with the subject automatically.

---

## 5. Guardrails + what you get for free

The texture is a *backdrop*, never a focal point. Keep every variant inside these lines:

- **Monochrome, low alpha.** `--hairline` (or ≤ 0.06 for the glow tint). No rainbow, no saturated gradients — a loud gradient backdrop is the "synthwave" anti-pattern (`SKILL.md` §5), the opposite of this aesthetic.
- **`transform`/`opacity` only** for any motion, with a `prefers-reduced-motion` off-switch (rule 9).
- **It must not fight charts or print** — and you get both for free, because every variant is `body::before` and the skill already ships:
  ```css
  /* tokens.md §1.6 — texture suppressed behind data-viz so it can't clash with gridlines */
  .chart-container, .plot-container, .canvas-container { isolation: isolate; background: var(--card); }
  /* tokens.md §5.4 — texture hidden when printing */
  @media print { body::before { display: none; } }
  ```
  You don't re-declare these; they already match `body::before` for any variant.

---

## 6. Adding a variant

1. Write one `html[data-bg="name"] body::before { background-image: …; }` block. Use `--hairline` for lines/dots; if it animates, oversize with `inset` and animate `transform` only.
2. If it animates, add the `@media (prefers-reduced-motion: reduce)` off-switch.
3. If it's subject-specific, name it in a preset's "paired `data-bg`" column in `foundations/palettes.md` §3.

No scaffold, no chart-exclusion, no print rule to copy — they are inherited from `tokens.md`.
