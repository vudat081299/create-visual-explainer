# Palettes — Subject Identity Themes

Every page in this skill defaults to the same green identity (eyebrow, title keyword, focus ring, links). A stack of such pages feels identical. This file gives a page a **subject-appropriate identity accent** — so a chemistry note reads differently from a probability note — *without* touching the semantic color system that charts and status chrome depend on.

Read this whenever you build a page and want it themed, which is the default (pick by subject). Pairs with `foundations/backgrounds.md` (the texture half of a theme set).

**Quick map**: §1 The identity token (L11–33) · §2 The law — identity changes, semantics don't (L34–57) · §3 Subject presets + CSS (L58–99) · §4 Selection protocol (subject-first, safe-random fallback) (L100–124) · §5 Adding a new subject (L125–137) · §6 WCAG for identity hues (L138–148)

---

## 1. The identity token

The page's "hero" hue lives in one variable pair, defined in `foundations/tokens.md` §1.3:

```css
:root {
  --accent-identity:     var(--accent-green);   /* default — unchanged pages stay green */
  --accent-identity-rgb: var(--accent-green-rgb);
}
```

Everything that expresses *page identity* reads this token (the wiring list is §2). A theme overrides only these two values, and one override re-skins the eyebrow, title keyword, focus ring, links, TOC active state, and progress bar at once. Because the default is `var(--accent-green)`, a page that sets no theme is byte-for-byte what the skill produced before this file existed.

You select a theme declaratively on the root element, mirroring `data-theme`:

```html
<html lang="en" data-theme="dark" data-accent="physics" data-bg="drift">
```

Omit `data-accent` (or set `="default"`) → green. The `data-bg` half lives in `foundations/backgrounds.md`.

---

## 2. The law — identity changes, semantics don't

This is the rule that keeps theming from breaking the rest of the skill. In this design system `--accent-green` (and the other five accents) carry **two different jobs**, and only one of them is themeable.

**Identity job (themeable → read `--accent-identity`).** The page's brand/voice; no data meaning. Swapping its hue per subject is the whole point of this file. Surfaces:

- eyebrow (`.eyebrow`) and the one highlighted title keyword (`.title span`) — `tokens.md` §2.4
- focus ring (`:focus-visible`) — `tokens.md` §6
- in-prose links, TOC active link + active border (`.toc-link[data-active]`), reading-progress fill, section-anchor hover — `usecases/longform-study.md`
- explainer control value + caption keyword, walkthrough progress number — `usecases/interactive-explainer.md`
- expand button + detail-panel resize handle, overview-map hover + index keys — `usecases/progressive-disclosure.md`

**Semantic job (NOT themeable → keep the named accent).** Here the color *is* the information; repaint it and the page lies. Leave these fixed across every subject:

- `--accent-green` = success / "good" / definitions; `--accent-red` = danger / error / "worse"; `--accent-yellow` = warning / proof; `--accent-blue` = info / theorem; `--accent-purple` = examples; `--accent-cyan` = code-output / sandbox
- every chart palette — `--cat-*`, `--seq-*`, `--div-*` (`tokens.md` §1.5) — recoloring these destroys categorical distinctness and the diverging zero-point
- status chrome: badges, toasts, meters, the comparison panel's red-vs-green deltas (`components/*`)

> Why not recolor everything per subject? Because a chemistry page where "success" is orange, "danger" is also orange, and the chart's first series is orange has no color language left — the reader can't tell a passing test from a failing one. Identity is decoration; semantics are data. Theme the first, freeze the second.

Practical rule: **do not** find-replace `--accent-green` → `--accent-identity` across a page. Switch only the identity surfaces above. When unsure, ask "if I recolor this, does any *meaning* change?" If yes, it's semantic — leave it.

---

## 3. Subject presets

Each preset sets the identity accent (dark + light) and names a paired background from `foundations/backgrounds.md`. Hues come from the six WCAG-verified accents wherever possible (so identity text stays readable for free); the one new hue, chemistry orange, is contrast-checked in §6.

| `data-accent` | Subject | Identity dark / light | Paired `data-bg` | Why this hue |
|---|---|---|---|---|
| *(unset)* / `default` | — | `#00e5a0` / `#008060` | `grid` | the skill's original look |
| `math` | Toán / abstract | `#3b82f6` / `#1d4ed8` | `grid` | ink on paper; calm, exact |
| `physics` | Vật lý | `#22d3ee` / `#0e7490` | `drift` | energy, waves, motion |
| `chemistry` | Hoá học | `#fb923c` / `#c2410c` | `dots` | flask amber; molecular dots |
| `probability` | Xác suất / Thống kê | `#c084fc` / `#7c3aed` | `dots` | scatter, uncertainty |
| `nature` | Giải thích tự nhiên | `#00e5a0` / `#008060` | `contour` | organic; topographic lines |
| `crypto` | Mật mã | `#f5d000` / `#936a00` | `scanline` | amber CRT terminal; lean mono |

`nature` reuses the green identity but pairs it with the contour background, so it still reads distinctly from a `default` page — the two-axis (color × texture) design means even a shared hue yields a different look. `crypto` leans into mono type (eyebrows, labels, a little more `--font-mono` in body accents) over the amber identity.

### CSS — drop these in once per page (or keep in a shared stylesheet)

Only non-default presets need an override; `:root` already holds the green default.

```css
/* DARK identity overrides (default theme) */
[data-accent="math"]        { --accent-identity: #3b82f6; --accent-identity-rgb: 59 130 246; }
[data-accent="physics"]     { --accent-identity: #22d3ee; --accent-identity-rgb: 34 211 238; }
[data-accent="chemistry"]   { --accent-identity: #fb923c; --accent-identity-rgb: 251 146 60; }
[data-accent="probability"] { --accent-identity: #c084fc; --accent-identity-rgb: 192 132 252; }
[data-accent="nature"]      { --accent-identity: #00e5a0; --accent-identity-rgb: 0 229 160; }
[data-accent="crypto"]      { --accent-identity: #f5d000; --accent-identity-rgb: 245 208 0; }

/* LIGHT identity overrides — darker hues to clear >=4.5:1 on #fafafa */
[data-theme="light"][data-accent="math"]        { --accent-identity: #1d4ed8; --accent-identity-rgb: 29 78 216; }
[data-theme="light"][data-accent="physics"]     { --accent-identity: #0e7490; --accent-identity-rgb: 14 116 144; }
[data-theme="light"][data-accent="chemistry"]   { --accent-identity: #c2410c; --accent-identity-rgb: 196 65 12; }
[data-theme="light"][data-accent="probability"] { --accent-identity: #7c3aed; --accent-identity-rgb: 124 58 237; }
[data-theme="light"][data-accent="nature"]      { --accent-identity: #008060; --accent-identity-rgb: 0 128 96; }
[data-theme="light"][data-accent="crypto"]      { --accent-identity: #936a00; --accent-identity-rgb: 147 106 0; }
```

Synonyms (`thống kê`/`stats` → `probability`, `hóa`/`hoá` → `chemistry`, `toán` → `math`, …) are resolved at selection time (§4), not with extra selectors — keep the CSS to the seven canonical names.

---

## 4. Selection protocol

Pick the identity in this order; the first that applies wins:

1. **User instruction wins, always.** "dùng màu xanh dương", "nền tĩnh", "đừng đổi màu" → obey verbatim. A stated preference overrides subject inference and overrides any earlier theme in the conversation.
2. **Infer from the subject** of the page:

   | Topic signals | Preset |
   |---|---|
   | calculus, algebra, proofs, linear algebra, số học, hình học, giải tích | `math` |
   | mechanics, electromagnetism, quantum, sóng, nhiệt, lực, vật lý | `physics` |
   | reactions, molecules, bonds, phản ứng, nguyên tố, hoá | `chemistry` |
   | distributions, Bayes, regression, sampling, xác suất, thống kê | `probability` |
   | biology, ecology, geology, weather, sinh học, tự nhiên, môi trường | `nature` |
   | hashing, ciphers, RSA, signatures, mã hoá, mật mã | `crypto` |
   | anything else / mixed / unclear | → step 3 |

3. **Curated random fallback.** When the subject gives no clear signal, pick *at random from the verified accents* — green, blue, cyan, yellow, purple (skip red as an identity, since red reads as danger) — and a tasteful background. **Do not** generate a random hex: arbitrary colors fail WCAG unpredictably and drift toward synthwave. Randomness is allowed *only* from the pre-verified set, so it varies the look without ever producing an unreadable or off-brand page.

Set `data-accent` (and the paired `data-bg`) on `<html>`. Don't narrate the mechanism to the reader; just ship the themed page.

> This is a styling *default*, not §2C "optional content" — you don't ask permission to theme a page the way you'd ask before adding a quiz. But because it's only a default, one word from the user ("xanh lá đi") redirects it.

---

## 5. Adding a new subject

The system is two-axis and declarative, so new subjects are cheap:

1. Pick an identity hue. Easiest: reuse one of the six accents (already WCAG-verified in both themes — `tokens.md` §6). For a *new* hue, compute its contrast as foreground text on `#0a0a0f` (dark) and `#fafafa` (light); keep both ≥ 4.5:1 (the eyebrow is small text — don't skip this).
2. Add one `[data-accent="name"]` block (dark) and one `[data-theme="light"][data-accent="name"]` block (light) to §3.
3. Add a row to the preset table and a topic-signal row to §4.
4. Optionally pair it with a background in `foundations/backgrounds.md`.

No component code changes — that is the point of routing identity through one token.

---

## 6. WCAG for identity hues

The identity accent is used as **small text** (the 11px mono eyebrow) and as a focus-ring outline, so every preset hue must clear AA for normal text (≥ 4.5:1) as foreground on the page background, in *both* themes. The five reused accents already do (`tokens.md` §6). The one added hue:

| Hue | On `#0a0a0f` (dark) | On `#fafafa` (light) |
|---|---|---|
| chemistry orange — `#fb923c` dark / `#c2410c` light | 8.7 : 1 ✓ AAA | 5.0 : 1 ✓ AA |

For reference, the reused identity hues (from `tokens.md` §6): green 12.0 / 4.7, blue 5.4 / 7.5, cyan 9.8 / 5.6, yellow 13.1 / 4.6, purple 7.4 / 6.4 — all ✓ AA or better in both themes.

Never paint the identity accent as foreground on `--card` or `--surface` without re-checking — those tints are not verified backgrounds (`tokens.md` §6).
