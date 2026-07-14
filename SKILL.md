---
name: create-visual-explainer
description: 'Build HTML study notes / interactive explainers / longform math tutorials in the Distill–3Blue1Brown–Ciechanowski lineage. Enforces both visual quality (Data-Dense Pro tokens, components, layout) AND content quality (layered pedagogy, plot integrity, math/code verification with trail). Trigger only when the user explicitly invokes this skill by name (e.g. "create-visual-explainer", "/create-visual-explainer", "dùng skill visual-explainer", "skill explainer"), or asks for a "tài liệu học/note bài giảng/study note/math note/interactive explainer" with clear intent to produce a structured HTML learning page. Do NOT trigger on generic "tạo trang HTML", "làm dashboard", "giải thích X" without explicit skill invocation — those are too broad. Do NOT use for code refactors, algorithm questions, plain emails, or iOS UI (use sister skill `industrial-data-ios`).'
---

# Create Visual Explainer

A multi-domain design system + content-quality framework for HTML study notes, learning materials, interactive explainers, longform tutorials, and dashboards. Aesthetic family: **Data-Dense Pro** (ClickHouse/Sentry/Grafana lineage) with **Terminal-Core** typography accents, dialed toward **SaaS Polish** (GitHub/Tailwind/Notion) when the task is longform reading or marketing rather than dashboards.

**Primary use case** is *learn-everything visualization* — explainer pages where the reader walks away understanding a concept they didn't 10 minutes ago. Pedagogy lineage: Distill.pub, 3Blue1Brown, Brilliant.org, Bartosz Ciechanowski.

The skill has **two pillars**:
- **Visual quality** — tokens, components, layouts (read these for any visual output)
- **Content quality** — pedagogy, plot integrity, math/code verification (read these for any educational/technical output)

A pretty page with wrong math is worse than an ugly page with right math. Both pillars matter. For iOS UI, use the sister skill `industrial-data-ios`.

---

## 1. Skill map — what each file does, when to load it

Read only what the task needs. Do NOT load all files up front. The skill is organized into **three buckets**, each with a different purpose:

- **`foundations/`** — design system tokens + content principles. Load gates for almost every task (`foundations/tokens.md` for any visual output; `foundations/pedagogy.md` + `foundations/verification.md` for any educational content).
- **`components/`** — reusable UI primitives, independent of usecase. Load only the ones the page actually contains.
- **`usecases/`** — recipes for specific page types (math tutorial, interactive explainer, longform study note). Each bundles the usecase-specific UI + structure + pedagogy reminders.

Each ref file has a `**Quick map**` line at the top with section line ranges — use `view_range` to read only the section you need instead of loading the full file.

### `foundations/` — nguyên lý xuyên suốt

| File | Role | Load when |
|---|---|---|
| `foundations/tokens.md` | Color / typography / spacing / motion tokens (dark + light) | Any visual output |
| `foundations/pedagogy.md` | HOW to explain (layered explanation, cognitive load, misconceptions) | **Baseline** for any educational content |
| `foundations/verification.md` | Pre-output verify (math, code, citation) + verification trail format | **Mandatory** for any math/code/factual claim in a deliverable |
| `foundations/plot-quality.md` | Pre-flight checklist for charts (annotations, axis ranges, lie detector, render bugs) + chart-type + palette quick reference (§9) | Page has charts |
| `foundations/palettes.md` | Subject identity palettes (Toán/Lý/Hoá/Xác suất/Tự nhiên/Mật mã) + safe randomization — changes the page's *identity* accent only; semantics stay fixed | Any page you want themed/varied (default: pick by subject) |
| `foundations/backgrounds.md` | Background-texture variants for the `body::before` signature layer (dots, crosshatch, drift, wave, contour, scanline, glow) — all reuse the tokens.md grid scaffold | Any page; pick a variant for visual variety |

### `components/` — UI tái sử dụng

| File | Role | Load when |
|---|---|---|
| `components/core.md` | Buttons, inputs, cards, tables, badges, modals, tooltips, code blocks, tabs, stat-card | Page has UI elements |
| `components/navigation.md` | Navbar, drawer, sidebar, breadcrumb, pagination, footer | Multi-page sites, app shells |
| `components/numeric.md` | Progress bar, meter, sparkline, count-up, stat-card grid | Page has numeric displays beyond a single stat |
| `components/dashboard-chrome.md` | Segmented control, filter chip, banner, avatar | Dashboard / admin / analytics chrome only |
| `components/asides.md` | Accordion/collapsible, sidenote, footnote, quiz, vocab tooltip | Educational pages with hide-show / aside content |
| `components/charts.md` | Chart container, axes, legend, tooltip, annotations, library glue, animation (rendering only — for chart-type selection see `foundations/plot-quality.md` §9) | Page has charts |
| `components/reader-tools.md` | Notes panel (local, context-aware, export/import), per-concept done-ticks + reading time, completion counter + jump-to-next-unread, top reading-progress bar, focus mode (dim non-active sections) | **Default for study pages with ≥5 concepts (§2E)** — attention/executive-function support |

### `usecases/` — skill cho từng loại trang

| File | Role | Load when |
|---|---|---|
| `usecases/math-learning.md` | KaTeX setup, theorem/proof boxes, equation containers, derivations | Math/DS tutorials, equation-heavy docs |
| `usecases/interactive-explainer.md` | Stage+controls, sliders, walkthroughs, sandbox (Distill-style interactive concept page) | Page has interactive demo |
| `usecases/longform-study.md` | TOC (sticky right rail), reading-progress bar, section anchor links | Long-form pages with ≥3 H2 sections |
| `usecases/progressive-disclosure.md` | Overview-first + brief/expand right detail panel (advance organizer + progressive disclosure): keep the main column skimmable, depth one click away | **Default reading layout for any multi-concept explainer (§2D)** — code, supplementary diagrams/examples, Layer-3 derivations and extra detail go in the right panel; main column stays the skimmable spine. Single short concept → inline `<details>` from `components/asides.md` instead |

### Other

| File | Role | Load when |
|---|---|---|
| `SKILL.md` | Router + immutable rules + clarification protocol + workflow | Always (this file) |
| `assets/starter-template.html` | Copy-paste boilerplate | Quick prototype |
| `MAP.md` | Vietnamese table-of-contents for the whole skill (human-facing) | When user wants an overview |

For iOS UI, switch to the sister skill `industrial-data-ios` — it carries the same tokens into Swift.

### Typical loadouts by task type

**Default learn-everything path** (typical "explain X" / "how does Y work" / "why does Z" — pick this when in doubt):
- Always: `foundations/tokens` + `components/core` + `foundations/pedagogy` + `foundations/verification`, and pick a theme set (`foundations/palettes` + `foundations/backgrounds`) so pages don't all look identical — default: infer from the subject
- **Progressive disclosure is the DEFAULT reading layout (§2D), not an opt-in.** Multi-concept page → `usecases/progressive-disclosure` (brief in the main column; code + supplementary diagrams/examples + Layer-3 + extra detail in the right detail panel). Single short concept → `components/asides` inline `<details>` collapsible for that same depth. Either way the essential teaching visual stays inline (rule 13).
- **Reader tooling is DEFAULT on study pages (§2E), not an opt-in.** ≥5 concepts → `components/reader-tools` (progress bar + done-ticks + completion counter + focus mode; notes panel if the page is a study/reference page the reader returns to).
- Add as needed: `usecases/math-learning` (equations) · `components/charts` + `foundations/plot-quality` (plots) · `usecases/interactive-explainer` (interactive) · `usecases/longform-study` (≥3 H2)

For other task types, pick the row that matches:

| Task type | Files to load |
|---|---|
| **DS learning page / longform study note** (multi-section, navigable) | `foundations/tokens` + `components/core` + `usecases/math-learning` + `usecases/longform-study` + `usecases/progressive-disclosure` (default for multi-concept — brief in main column, depth in right panel; §2D) + `components/reader-tools` (≥5 concepts; §2E) + `components/asides` + `foundations/pedagogy` + `foundations/verification` (+ `usecases/interactive-explainer` if interactive, + `components/charts` + `foundations/plot-quality` if charts) |
| **Interactive concept explainer** (no longform prose) | `foundations/tokens` + `components/core` + `usecases/math-learning` + `usecases/interactive-explainer` + `foundations/pedagogy` + `foundations/verification` |
| **Dashboard / analytics UI** | `foundations/tokens` + `components/core` + `components/charts` + `components/numeric` + `components/dashboard-chrome` + `components/navigation` + `foundations/plot-quality` |
| **Marketing / landing / blog post** | `foundations/tokens` + `components/core` (+ `components/navigation` if has nav) |
| **Trivial inline HTML** | None — use §4 cheatsheet directly |
| **Math/code claim, chat-side** (not building a page) | `foundations/verification` only |

---

## 2. Two sets of rules

### 2A. Visual immutable rules (10 rules)

These give the design 80% of its visual coherence. If they conflict with what the user asks, briefly ask before deviating.

1. **Triple-opacity rule.** For chip/badge/pill chrome, every accent at solid (text/icons), 0.08–0.10 (bg), 0.20–0.25 (border) — no fourth value. Sanctioned exceptions (different use cases, NOT chip chrome): focus rings 0.15–0.30, modal backdrops 0.60–0.85, section-highlight box bg 0.05–0.07. See `foundations/tokens.md` §1.4.
2. **3-tier surface depth, no shadows for chrome.** `--bg → --surface → --card`. `box-shadow` only for true overlays.
3. **Tabular numerals everywhere numeric.** Tables, stat cards, chart axes, code, `input[type=number]`.
4. **Distinctive typography stack — never substitute.** Bebas Neue + DM Sans + JetBrains Mono.
5. **Eyebrow → giant title → small subtitle composition** for every page header. Exactly ONE accent-colored keyword in the title.
6. **No decorative gradients in chrome.** Gradients allowed only inside data-viz containers.
7. **WCAG-verified text only.** `--text-dim` is decorative-only (≥18pt or ≥14pt-bold).
8. **Triple-opacity for state colors too** (mirror of rule 1). Success/warning/danger/info follow the same `solid / 0.08–0.10 / 0.20–0.25` pattern.
9. **Animate `transform` and `opacity` only.** Respect `prefers-reduced-motion: reduce`.
10. **Prefer light mode opt-in, not auto.** Default is dark; light is `[data-theme="light"]`.

### 2B. Content immutable rules (6 rules)

These determine whether the content actually teaches what it claims to teach.

11. **Layered explanation, never formalism-first.** Start with cartoon (Layer 1), then mechanism (Layer 2), then formula (Layer 3). Reader earns each Greek letter. (See `foundations/pedagogy.md` §2.)
12. **Concrete before abstract — always.** Worked numerical example before the general theorem. Greek letter + number on the next line. (See `foundations/pedagogy.md` §3.)
13. **Comprehension-first form.** Pick whichever form gives the reader the fastest path to understanding — analogy, story, diagram, plot, worked number, table, code, interactive demo. Pick by load on working memory, not by aesthetic. Visuals are mandatory only when the concept has spatial/temporal/quantitative structure prose can't carry. (See `foundations/pedagogy.md` §7. Lineage: Feynman, Ciechanowski, 3Blue1Brown.)
14. **Every plot has axis labels (with units), title or annotation, and at least one stated finding.** A pretty unlabeled chart is decoration, not data. (See `foundations/plot-quality.md` §2.)
15. **Verify before output.** For any math/code/factual claim: re-derive at least once, plug numbers back into formulas, trace code on small input, check sign + units + limit cases. (See `foundations/verification.md` §1.)
16. **End every deliverable with a verification trail line.** Applies when handing off a deliverable (page, document, code, math derivation, dataset, file). State what was verified, what was caught and fixed, what remains uncertain. Don't claim verification you didn't perform. *Pure conversational replies (clarification, brief Q&A, picking a color) don't require a verification trail.* (See `foundations/verification.md` §8.)

---

## 2C. Clarification protocol & content scope

The user has explicitly opted INTO being asked before generating. Walk this before producing any deliverable.

### Step 1 — Ask multi-choice questions when

- Request is missing info to choose between materially different outputs (level, language, tone)
- You're considering optional content beyond what user explicitly asked (see Optional list below)
- Two reasonable interpretations exist and picking wrong wastes the user's time

Do NOT ask when the request is fully specified, the default is safe (dark vs light → dark), or the Required list below already covers it.

### Step 2 — How to ask

Multi-choice format only, NOT open-ended. **Max 3 questions × 2-4 mutually-exclusive options each.** Vietnamese if user wrote in Vietnamese.

Worked example for "Tạo trang giải thích linear regression":

> 1. **Mức độ kỹ thuật?** [a] Cartoon-first · [b] Cân bằng (cartoon → mechanism → formula) · [c] Formal-heavy (theorem + proof)
> 2. **Interactive demo?** (slider slope/intercept để thấy MSE thay đổi) [a] Có · [b] Không
> 3. **Optional thêm (chọn nhiều)?** [ ] Quiz · [ ] Worked example với data thực · [ ] Code Python/sklearn · [ ] Không thêm gì

### Step 3 — On skip ("vô đi" / "làm luôn" / silence)

Required-only content (below), no optional extras, dark mode, language matching user.

### Required content — always include

- Page header: eyebrow + title + subtitle (per `foundations/tokens.md` §2.4)
- Layered explanation: Layer 1 → 2 → 3 (rule 11)
- One worked numerical example before each formal theorem (rule 12)
- Comprehension-first form choice (rule 13) — visual when structure demands it, prose when the concept is linguistic
- Misconception warning if topic has a common one (`foundations/pedagogy.md` §4)
- Axis labels + title + caption on every chart (rule 14)
- TOC when page has ≥3 H2 sections — right-rail sticky for ≥6, inline list-at-top for 3-5 (`usecases/longform-study.md` §1)
- Progressive-disclosure layout (§2D) — brief in the main column; code, supplementary diagrams/examples, Layer-3 and extra detail in the right panel (multi-concept) or inline `<details>` (single concept). Essential teaching visual stays inline.
- Reader-tooling layer (§2E) when the page has ≥5 concepts — reading-progress bar + per-concept done-ticks & reading time + completion counter with jump-to-next-unread + focus mode. Notes panel too when it's a study/reference page the reader returns to (`components/reader-tools.md`)
- Verification trail line on the deliverable handoff (rule 16)

### Optional content — only if user explicitly requests

Quiz · footnotes (unless citation is the point) · vocabulary tooltips · sidenotes (Tufte-style) · multiple worked examples (>1) · interactive code playground (editable/runnable sandbox) · comparison panels (A/B) · "next steps" / "recommended reading" · section anchor `#` on hover · count-up animations · decorative SVG with no pedagogical content.

**Rule of thumb**: if the user did not name it, did not imply it, and rules 11-15 don't require it — ask before including.

**Note — disclosure layout is NOT in this gate.** §2C decides *whether* an optional extra exists at all. It does **not** gate *where* committed depth lives: per §2D, code listings, supplementary diagrams, Layer-3 derivations, edge cases and extra detail the page already commits to are disclosed (right panel / inline collapsible) **by default** — no need to ask.

**Note — reader tooling is NOT in this gate either.** Progress bar, done-ticks, completion counter, focus mode and the notes panel are *accessibility infrastructure* (§2E), not content. Ship them by default on study pages; don't ask permission to make a page readable.

---

## 2D. Progressive disclosure — the default reading layout

The main column carries the **full** explanation needed to follow the argument: Layer 1 (intuition) + Layer 2 (mechanism) + the one key formula per concept + the essential teaching visual. Everything heavier is **disclosed on demand by default** — you do NOT ask the user first:

- code listings / snippets
- supplementary or reference diagrams (NOT the core teaching visual — that stays inline, rule 13)
- Layer-3 derivations, algebra, proofs
- edge cases, long reference tables
- "extra info" the page already commits to

**Mechanism by page size:**
- **Multi-concept page** → right detail panel (`usecases/progressive-disclosure.md`): one panel, one section open at a time, drag-to-resize.
- **Single short concept** → inline `<details>` collapsible (`components/asides.md`): no right rail.

**Granularity — concept-level *or* block-level.** A trigger may reveal a *whole concept's* depth (one trigger per concept) **or** a *single heavy block* (one trigger per code listing / supplementary box / reference table), with several triggers in one concept sharing the one panel. Reach for block-level when the depth tier is itself long — e.g. a page with a separate "detail" reading mode (simple/detail tabs, or a long deep section): the reader is already past the brief, but code and supplementary material still go one click away, not dumped inline. Pick the grain that keeps the currently-scrolled surface skimmable. (Multiple triggers per concept → `usecases/progressive-disclosure.md` §4.)

**"Skimmable spine" is per reading-mode.** The spine is whatever the reader scrolls *by default in the mode they're in* — the briefs in a one-column page, but also the detail-mode body when a page has a simple/detail split. Code and supplementary material stay one click from *that* surface even inside a detail mode; a "detail tab" is not a licence to inline code. Disclosure nests.

**Self-sufficiency test (hard rule).** Reading only the main-column briefs top-to-bottom, opening nothing, the reader must still follow the whole argument. If a brief needs an opened panel to make sense, pull that piece up into the brief. Inversely, never put anything in the panel whose absence makes the brief *wrong* — the panel only goes *deeper*. At **block level** the same law gains one clause: never disclose a concept's *only* substantive content — if removing the block leaves an empty heading + a button, keep it inline (that is *hiding*, not disclosure). (Full law: `usecases/progressive-disclosure.md` §2.)

**This is the spatial form of rules 11 + 13** — it gives the pedagogy layers a fixed place on the page; it does not override `foundations/pedagogy.md`. It is also distinct from §2C: §2D makes the disclosure *layout* the default; §2C still gates whether *optional extras* (quiz, footnotes, interactive playground, >1 worked example, A/B panels) exist at all.

---

## 2E. Reader tooling — the default attention-support layer

Rules 11-13 make the *content* learnable. §2D gives the depth a place to live. This section covers the third axis: whether the reader can **sustain attention long enough to finish**. On a dense page that is not a nicety — it's the difference between a page that gets read and a page that gets bookmarked forever.

Ship these **by default on any study page with ≥5 concepts** (`components/reader-tools.md`). Do not ask first (§2C).

| Tool | Answers |
|---|---|
| Top reading-progress bar | "is this endless?" |
| Per-concept done-tick + `~4 phút` reading time | "have I read this?" / "do I have time right now?" |
| Completion counter `7/23 đã đọc` + **jump-to-next-unread** | "where was I?" — the highest-value button for an interrupted reader |
| **Focus mode** — dims every section except the one under the reading line | "everything on screen is shouting" |
| **Notes panel** — local, auto-attached to the current concept, `.md`/`.json` export + merge-import | "I had a thought and lost it" |

Notes panel is default on study/reference pages the reader returns to; skip it on a single-sitting explainer.

**Three laws** (full argument + code: `components/reader-tools.md` §1):

1. **Reader-controlled, never automatic.** Focus mode is off until toggled. Ticks are set by the reader — *scrolling past a section is not reading it*, and a page that auto-ticks destroys the only thing the counter has: the reader's trust in it.
2. **Additive, never subtractive.** Dim with `opacity` + `saturate()`, never `blur()` (futile eye accommodation, wrecks low-vision reading) and never `display:none`/`opacity:0` (breaks `Ctrl+F`, screen readers, and looks like a bug). Distinguish from §2D: progressive disclosure *hides* depth — an authoring-time content decision. This layer only *de-emphasizes* — a reading-time view decision.
3. **State is local, guarded, and exportable.** Every `localStorage` call sits in `try/catch` — it throws in private mode and **is unavailable in the claude.ai artifact sandbox**, where an unguarded call kills every script after it. Losing state must degrade the page to "a normal page", never to a broken one. Notes always export.

All of this joins on the `[data-concept]` contract (`components/reader-tools.md` §2), the same attribute the detail panel uses.

**Artifact caveat.** If you hand off an artifact rather than a downloadable `.html`, say in the verification trail (rule 16) that notes and ticks persist for the session only, and point the reader at `Xuất .md`.

---

## 3. Workflow for a typical educational HTML page request

1. **Clarify first** (§2C) — if request is ambiguous or optional content is being considered, ask multi-choice questions BEFORE generating. Skip = required-only content.
2. **Pick scope and files** — from §1 Skill Map. Baseline for learning content is `foundations/pedagogy.md` + `foundations/verification.md`.
3. **Read content references before visual** (`foundations/pedagogy.md`, `foundations/plot-quality.md`, `foundations/verification.md`). Content rules organize the page; visual rules just style it.
4. **Plan content + disclosure split** — apply rules 11-13: pick comprehension-first form (rule 13), layer the explanation Layer 1→2→3 (rule 11 + `foundations/pedagogy.md` §2), one worked number before each formal claim (rule 12), identify likely misconception (`foundations/pedagogy.md` §4) and plan a `.warning` box. Then apply §2D: for each concept decide what stays in the brief (Layer 1+2 + key formula + essential visual) vs. what goes to the disclosure tier (code, supplementary diagrams, Layer-3, edge cases, extra detail). Pick the mechanism: right panel (multi-concept) or inline `<details>` (single concept).
5. **Plan navigation + reader tooling** — ≥3 H2 sections → TOC required (right-rail for ≥6, inline list for 3-5; `usecases/longform-study.md`). ≥5 concepts → reader-tooling layer required (§2E, `components/reader-tools.md`): give every markable unit a stable `data-concept` id *now*, since ticks, focus scroll-spy, notes and the detail panel all join on it.
6. **Pick starting point + theme set.** Fresh page → start from `assets/starter-template.html`. Editing existing page → skip the template; read visual references for any element already present. Then pick a **theme set** so pages don't all look the same: set `data-accent` (identity palette) + `data-bg` (background texture) on `<html>` — default is to infer from the subject (math→blue, physics→cyan, chemistry→orange, probability→purple, nature→green+contour, crypto→amber+scanline; unclear→curated random). User choice always wins. See `foundations/palettes.md` + `foundations/backgrounds.md`. This only changes the *identity* accent — semantic colors (success/danger/chart palettes) stay fixed.
7. **Pick the output channel.** HTML output from this skill is almost always longform (multi-section, has TOC/sliders/charts) — output as an **HTML artifact** so the user can view/download/edit it. Inline visualizer (`visualize:show_widget`) is OK *only* when the page is small (≤300 lines, single concept, no TOC) AND fits the flow of inline scrolling. Never embed the full page as a code block in chat.
8. **Apply quality checklists during build** — rule 14 + `foundations/plot-quality.md` §2 for every plot; rule 15 + `foundations/verification.md` §1 for every formula, numerical claim, or code snippet.
9. **Self-test before output** — walk these checklists in order: `foundations/pedagogy.md` §9 (pedagogy/content) → `foundations/plot-quality.md` §2 (every plot) → `foundations/verification.md` §1 + §8 (math/code/factual + handoff). Confirm no unsolicited optional content (§2C), and run the §2D self-sufficiency test (briefs readable alone; the core teaching visual is inline, not hidden). If the page ships reader tooling (§2E), confirm: focus mode off by default, no auto-ticking, dim-not-blur, every `localStorage` call guarded, notes exportable.
10. **End with verification trail line** when handing off the deliverable (rule 16). Skip on pure clarification replies.

---

## 4. Quick token cheatsheet

Most-used tokens — Claude can use them inline without loading `foundations/tokens.md` for trivial cases. For full palette, light mode, chart palettes (categorical 4-10, sequential, diverging), grid texture, motion → read `foundations/tokens.md`.

```css
/* Surfaces (dark mode default) */
--bg: #0a0a0f; --surface: #111118; --card: #16161f; --border: #2a2a3a;

/* Text — WCAG verified on --bg */
--text-primary: #f0f0f8;    /* 17.4:1  body, headings */
--text-secondary: #a8a8c0;  /*  8.5:1  secondary copy */
--text-muted: #8888a8;      /*  5.8:1  captions, labels */
--text-dim: #5a5a78;        /*  3.0:1  decorative ONLY (large text) */

/* Accents — primary 6 */
--accent-green:  #00e5a0;  /* success, primary CTA, definitions */
--accent-blue:   #3b82f6;  /* info, links, theorems */
--accent-yellow: #f5d000;  /* warning, highlight, proofs */
--accent-red:    #ff4560;  /* danger, warnings */
--accent-purple: #c084fc;  /* secondary, examples */
--accent-cyan:   #22d3ee;  /* code-output, sandbox */

/* Identity accent (page "hero" hue) — defaults to green; theme per subject via foundations/palettes.md */
--accent-identity: var(--accent-green); --accent-identity-rgb: var(--accent-green-rgb);

/* Chart palette — first 3 categorical (most-used). For 4-10 see tokens.md §1.5 */
--cat-1: #00e5a0; --cat-2: #3b82f6; --cat-3: #f5d000;

/* Spacing scale (4px base) — most-used. Full scale in tokens.md §3.1 */
--sp-2: 8px;    /* pill/badge padding */
--sp-3: 12px;   /* input/button vertical padding */
--sp-4: 16px;   /* input/button horizontal padding, default gap */
--sp-5: 20px;   /* card vertical padding */
--sp-6: 24px;   /* card horizontal padding */
--sp-12: 48px;  /* between major sections */

/* Radius — most-used */
--r-sm: 6px;    /* buttons, inputs */
--r-md: 10px;   /* cards, note boxes */
--r-pill: 9999px;

/* Fonts */
--font-display: 'Bebas Neue', 'Oswald', 'Arial Narrow', sans-serif;
--font-body: 'DM Sans', system-ui, sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', 'SF Mono', monospace;
```

**Google Fonts (one request):**
```html
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;700&family=JetBrains+Mono:wght@400;500&display=swap">
```

### Aesthetic dial — dense vs polished

The same tokens can be tuned toward two ends. Pick one mode per page; don't mix on the same surface.

| Dial | Dense industrial (default — ClickHouse/Sentry/Grafana) | Polished SaaS (GitHub/Tailwind/Notion) |
|---|---|---|
| Section gap | `--sp-12` (48px) | `--sp-20` to `--sp-32` (80–128px) |
| Accents per page | 4–6 (charts, badges, statuses) | 1–2 (one CTA color, one info color) |
| Mono usage | Labels, axes, eyebrows, status pills, code | Code only |
| Background grid | On | Off (or `--hairline` even subtler) |
| Body font weight | 400 with 500 for emphasis | 400 only, looser line-height (1.7+) |
| Card density | `--sp-5 --sp-6` padding | `--sp-8 --sp-10` padding |

**Pick mode by task:** dashboards / data-viz / technical docs → dense. Marketing / landing / blog post / longform reading → polished. Math/DS study notes are usually polished body with dense theorem/equation boxes — fine to mix box-types but keep prose airy.

---

## 5. Anti-patterns (what NOT to do)

### Visual anti-patterns

- ❌ Centered hero + 3 feature cards (generic AI-slop layout) → use asymmetric columns, oversized numerals, eyebrow composition
- ❌ Inter / Roboto / system-ui as primary font → breaks the aesthetic point of view
- ❌ Purple gradients on dark backgrounds → "synthwave", not "industrial flat"
- ❌ `box-shadow` for card elevation → use `--bg → --surface → --card` layers
- ❌ Inventing a 4th accent opacity for chip chrome (e.g. 0.15) → stick to solid / 0.08–0.10 / 0.20–0.25 (focus rings + overlays are sanctioned exceptions, see `foundations/tokens.md` §1.4)
- ❌ `--text-dim` for normal-size body copy → fails WCAG; use `--text-muted` minimum
- ❌ More than 10 categorical chart colors → split, facet, or filter
- ❌ Emoji as production icons → use Lucide / Tabler / Heroicons
- ❌ Forgetting tabular numerals → misaligned numeric columns read as broken
- ❌ Animating `width` / `height` / `top` / `left` → use `transform` and `opacity`
- ❌ Recoloring the six *semantic* accents to match a subject theme (making "success" orange on a chemistry page) → only the *identity* accent (`--accent-identity`) changes per subject; semantics stay fixed or charts/status lie. See `foundations/palettes.md`
- ❌ **`filter: blur()` on body text to de-emphasize it** (focus mode) → forces futile eye accommodation, wrecks low-vision reading; dim with `opacity` + `saturate()`. Blur belongs on modal backdrops only. See `components/reader-tools.md` §6
- ❌ Loud / rainbow / synthwave animated background → backgrounds stay monochrome `--hairline`, ≤~0.06 alpha, and animate `transform` / `opacity` only. See `foundations/backgrounds.md`

### Content anti-patterns

- ❌ Leading a section with a formula (formalism-first) → start with the question the formula answers
- ❌ Introducing more than 3 new terms in one section → split sections
- ❌ Greek letter without a numerical example on the very next line → reader has nothing to anchor to
- ❌ "Clearly", "trivially", "it is well known that" → assume nothing; either show or cite
- ❌ Citation without author + year + venue → either complete it or remove it
- ❌ Plot without axis labels including units → looks pretty, teaches nothing
- ❌ Bar chart with truncated y-axis → distorts magnitudes; use line chart or include zero
- ❌ Dual y-axis chart → implies false correlation; use side-by-side panels instead
- ❌ Claiming numerical values from memory → recompute, even if "obvious"
- ❌ **Forcing a visual when prose is shorter and clearer** → violates rule 13 (visual-first ≠ comprehension-first); pick the form that takes the reader the shortest path
- ❌ **Forcing prose when the concept is spatial/temporal/quantitative** → also violates rule 13; if a diagram or animation makes it click in 3 seconds, use it
- ❌ Ending a deliverable handoff without a verification trail line → user explicitly requested this for tài liệu
- ❌ **Adding a quiz / interactive playground / footnote / vocab-tooltip without the user asking** → violates §2C; these stay optional and require explicit request (note: code, supplementary-diagram and Layer-3 *disclosure* is DEFAULT per §2D, not gated)
- ❌ **Skipping the §2C clarification protocol** when the request is ambiguous → user prefers being asked over being guessed at
- ❌ **No TOC on a page with ≥3 H2 sections** → user prefers TOC by default for navigable docs
- ❌ **Dumping code, full derivations or long reference tables inline in the skimmable spine** → violates §2D; push them to the detail panel or inline `<details>` so the spine stays skimmable. The spine is *per reading-mode*: even inside a *detail* tab/section, code and supplementary blocks stay one click away (block-level disclosure), not inlined
- ❌ **Hiding the core teaching visual behind a click** → violates rule 13 + §2D; only *supplementary* diagrams get disclosed — the essential spatial/temporal visual stays inline
- ❌ **A brief that can't be understood without opening its panel** → fails the §2D self-sufficiency test; pull the missing piece up into the brief
- ❌ **Disclosing a concept's *only* substantive content** → leaves an empty heading + a button; that's hiding, not disclosure. If the block is all the concept has, keep it inline (same spirit as never hiding the core teaching visual)
- ❌ **Auto-ticking a section as "read" because the reader scrolled past it** → scrolling ≠ reading; a false completion count is worse than none (§2E law 1)
- ❌ **Focus mode enabled by default, or `display:none`/`opacity:0` on non-active sections** → breaks `Ctrl+F` and screen readers, and takes the page away from a reader who never asked (§2E law 2)
- ❌ **Unguarded `localStorage`** in a page that ships notes/ticks → throws in private mode and in the claude.ai artifact sandbox, silently killing every script below it (§2E law 3)
- ❌ **A notes panel with no export, or an import that replaces instead of merges** → the reader's own thinking is trapped in a browser profile, or destroyed by one mis-click
- ❌ **Reading-time estimate computed over KaTeX and `<pre>`** → a page of derivations reports "2 phút"; strip math and code before counting words
- ❌ **Shipping a bare reading-progress bar as the only progress signal** → it measures scroll, not comprehension; pair it with the `x/y đã đọc` counter and jump-to-next-unread
- ❌ **A dense study page (≥5 concepts) with no reader tooling at all** → violates §2E; attention support is infrastructure, not an optional extra

---

**Aesthetic family**: Data-Dense Pro (default) / SaaS Polish (longform & marketing) · **Closest visual siblings**: ClickHouse, PostHog, Sentry, Grafana, Supabase, GitHub, Tailwind, Notion, Linear
**Pedagogy lineage**: Distill.pub, 3Blue1Brown, Brilliant.org, Bartosz Ciechanowski, Feynman
**Sister skill**: `industrial-data-ios` for iOS UI (same tokens, Swift mirror)
