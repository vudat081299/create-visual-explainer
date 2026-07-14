# CHANGELOG-archive — create-visual-explainer

> Archived early-evolution entries (v1.1–v1.4). The active CHANGELOG starts at v1.5 in `CHANGELOG.md`. v1.5 is the "learn-everything reframe" cut-line — entries before it are kept here for historical context; the v1.5+ era is what's actually relevant for understanding the current skill.

---

## v1.4 (2026-05-05) — Aesthetic dial + cheatsheet expansion + loadout examples

User-requested additions on top of v1.3 cleanup. No breaking changes; all existing rules and references preserved verbatim.

### Added — Secondary aesthetic lineage (GitHub / Tailwind / Notion)

User likes the polish of GitHub, Tailwind, and Notion alongside the existing ClickHouse/Sentry/Grafana density. Skill now explicitly supports both modes:

- **Description (frontmatter)** — adds "with GitHub / Tailwind / Notion polish for longform and marketing"
- **Intro paragraph** — names "SaaS Polish" as a second mode, dialed in for longform/marketing
- **§3 new subsection "Aesthetic dial — dense vs polished"** — concrete token guidance: section gap, accent count, mono usage, background grid, card density, pick-by-task routing
- **Closing "Aesthetic family" line** — siblings expanded with GitHub, Tailwind, Notion, Linear

The two modes share the same tokens — only their values dial. No new files, no new components, no rule changes. Picking which mode is a per-task decision (dashboards → dense; marketing/blog → polished; DS study notes → polished body + dense theorem boxes).

### Updated — `SKILL.md` §3 Quick token cheatsheet expanded

For task-saving (refinement (a) from v1.3 review). Added inline so trivial-medium tasks can skip loading `tokens.md` (589 lines):

- Categorical chart palette top 3 (`--cat-1`, `--cat-2`, `--cat-3`)
- Spacing scale top 6 (`--sp-2` through `--sp-12`) with usage hints
- Radius top 3 (`--r-sm`, `--r-md`, `--r-pill`)

Cost: ~12 added lines in SKILL.md. Saving: removes a forced `tokens.md` load for any task that needs spacing or chart-color tokens but not full palette.

### Updated — `SKILL.md` §1 Typical loadouts is now a table

Was: one prose line for "DS learning page". Now: 7-row table covering all major task types. Helps Claude route faster:

- DS learning page / longform study note
- Interactive concept explainer (no longform)
- Dashboard / analytics UI
- iOS screen / Swift UI
- Marketing / landing / blog post
- Trivial inline HTML
- Math/code claim, chat-side

### Net effect

- SKILL.md: 207 → ~253 lines (still well under 500-line budget)
- Cheatsheet expansion: trivial visual tasks now self-contained
- Aesthetic dial: skill works for non-dashboard/non-DS tasks without losing identity

---

## v1.3 (2026-05-05) — Tinh gọn pass

User-requested cleanup focused on reducing context cost and removing redundancy. No behavioral changes — every immutable rule, every reference file, every eval criterion preserved.

### Removed
- Duplicate `SKILL.md` and `CHANGELOG.md` at the root of the zip (artifact of macOS zip including parent folder)
- macOS junk (`.DS_Store`, `__MACOSX/._*`)

### `SKILL.md`
- **Description (frontmatter) shortened** from ~290 words to ~115 words. Cut redundant trigger-phrase enumeration; kept core "what + when + don't". Reduces every-conversation context cost.
- **§2C and §2D merged** into a single section "Clarification protocol & content scope". Required/Optional content lists preserved verbatim; clarification-protocol structure tightened from 3 nested H3s to 3 numbered steps. Worked Vietnamese example kept. All references to §2D in §4/§5 retargeted to §2C.

### `references/` — TOC added at top of files >400 lines
A one-line "Quick map" pointer added to each, so Claude can jump to the relevant subsection without scrolling the whole file:
- `tokens.md` (587 lines)
- `components.md` (672 lines)
- `patterns-learning.md` (642 lines)
- `patterns-navigation.md` (579 lines)
- `patterns-data-display.md` (558 lines)

### Trimmed redundant content
- `pedagogy.md` §10 "19-year-old test" merged into §9 as a closing subsection (was a standalone H2 saying mostly the same thing)
- `verification.md` §10 "would I bet on this?" merged into §9 minimum-viable-verification (same pattern — operationalizes the same idea)
- `patterns-math.md` §7 "Pedagogy reminders" replaced with a 3-sentence cross-reference to `pedagogy.md` (was duplicating content from there)

### Not changed
- All 15 immutable rules (10 visual + 5 content) — verbatim
- All trigger phrases that were content-bearing (mục đích vẫn intact)
- `evals/evals.json` — all 4 eval cases and their criteria
- `assets/starter-template.html`
- All routing in `SKILL.md` §1 Skill map
- Cross-references between reference files

### Net effect
- SKILL.md: 251 → 206 lines
- Total skill bytes: ~194KB → ~190KB
- Trigger description: 290 → 115 words (visible in every conversation)
- Behavior: identical

---

## v1.2 (2026-05-04) — User-control protocol + skill consolidation

### Added — Clarification protocol (`SKILL.md` §2C)

User explicitly requested: AI must ask before adding any optional content; if user skips → default to required-only.

- **§2C "Clarification protocol"** — required step BEFORE generating
  - Lists when to ask (missing info, considering optional content, ambiguous request)
  - Lists when NOT to ask (fully specified, trivial defaults)
  - Specifies multi-choice format: max 3 questions × 2-4 options, mutually exclusive
  - Worked example in Vietnamese for a linear regression page request
  - Default-on-skip behavior: required-only content, dark mode, Vietnamese if user wrote VN

### Added — Required vs Optional content lock (`SKILL.md` §2D)

The bloat-prevention rule. Hard line between content the AI must include vs content that needs explicit user request.

- **Required (always)**: page header, layered explanation, worked example before theorem, misconception warning if applicable, axis labels on charts, **TOC when ≥3 H2 sections** (right-rail for ≥6 sections, inline-at-top for 3-5), verification trail line
- **Optional (require explicit ask)**: quiz, footnotes (unless citation purpose), vocab tooltips, "going deeper" collapsibles, sidenotes, multiple worked examples, code playground, comparison panels, "next steps", reading-progress bar, anchor-link-on-hover, count-up animations, decorative SVG

### Consolidated — `SKILL.md` §1 Skill map

Merged old §1 (visual + content references routing) and old §6 (file structure tree) into a single table at §1. Each row now has: file path, what it does, when to load. Old fragments cut.

- Eliminates redundancy between two sections that listed the same files
- One source of truth for "what files exist + when to read them"
- Old §6 deleted entirely

### Updated — `SKILL.md` §4 Workflow

- Step 1 is now **clarification protocol** (was: decide scope)
- New step 7: decide TOC variant (right-rail vs inline) based on H2 count
- New step 13: confirm no unsolicited optional content before sending

### Updated — `SKILL.md` §5 Anti-patterns

Three new content anti-patterns catching the bloat behaviors:
- Adding quiz/sidenote/deep-dive without user asking
- Skipping clarification protocol on ambiguous requests
- No TOC on a page with ≥3 H2 sections

Visual anti-pattern about "4th accent opacity" updated with the focus-ring + overlay exception note (consistent with v1.1's `tokens.md` §1.4 update).

### Why these changes

User reported 2 specific frustrations:
1. AI sometimes adds quizzes / extras they didn't ask for and don't remember being asked about
2. AI guesses when info is missing instead of asking multi-choice

Plus 1 architectural ask:
3. Skill should have a "table of contents" and so should generated docs

All three addressed in this version. No new files added — purely consolidation + protocol additions in `SKILL.md`.

### Not changed

- All 13 reference files in `references/` untouched (already good)
- All 5 immutable content rules (rules 11-15) untouched
- `assets/starter-template.html` untouched
- `evals/evals.json` from v1.1 untouched (already covers content quality via longform-study-note-bayes eval)

---

## v1.1 (2026-05-04) — Audit, fix, extend

### Fixed (bugs found in v1.0)

1. **`references/patterns-navigation.md` §1** — `background: rgb(var(--bg) / 0.85)` was invalid CSS. `--bg` is a hex value (`#0a0a0f`), not RGB channels — you cannot pass it to `rgb()`. The original file self-noted the issue and provided a fallback to solid `var(--bg)`. Now fixed properly using the new `--bg-rgb` token.

2. **`references/tokens.md` §1.1** — Added RGB channel triplet tokens for surfaces:
   - `--bg-rgb`, `--surface-rgb`, `--card-rgb`, `--border-rgb`
   - Both dark and light values defined
   - Required for any translucent overlay or backdrop-blur pattern

3. **`SKILL.md` Immutable Rule #1 ("Triple-opacity")** — Rule said "no fourth value" but the bundle's own `components.md` uses `0.15` for input focus rings and `0.30` for button focus rings. Added explicit "sanctioned exceptions" table:
   - Focus rings: 0.15–0.30
   - Modal backdrops: 0.60–0.85
   - Section-highlight box backgrounds: 0.05–0.07
   - The triple-opacity constraint is now scoped specifically to chip/badge/pill chrome (where it actually matters for visual consistency).
   - Same clarification mirrored in `tokens.md` §1.4.

### Added — `references/patterns-learning.md`

Components for long-form study notes and Brilliant.org-style interactive learning content:

- §1 **Table of Contents (sticky right rail)** — with IntersectionObserver auto-highlight
- §2 **Accordion / Collapsible** — `<details>` based, with `solution / hint / proof / aside` variants. Includes exclusive-open accordion group (using native `name` attribute, Chrome 120+/Safari 17.4+)
- §3 **Sidenote (Tufte-style margin note)** — float right above 1100px, collapse to inline below
- §4 **Footnotes** — page-bottom citations with back-links
- §5 **Quiz / Self-check** — multiple choice with feedback, disables after answer
- §6 **Reading-progress bar** — fixed top-of-viewport scroll indicator
- §7 **Section anchor link** — `#` symbol on hover for direct linking
- §8 **Vocabulary highlight + glossary tooltip** — hover-to-define inline terms
- §9 **Pedagogy tie-in** — table mapping pedagogy.md goals to components

### Added — `references/patterns-data-display.md`

Numeric primitives beyond the basic `.stat` card:

- §1 **Progress bar** (linear, with status variants + indeterminate state)
- §2 **Meter** (semantic — uses native `<meter>` element with low/high/optimum)
- §3 **Sparkline** (inline SVG mini-chart, with helper to generate points from data)
- §4 **Count-up animation** (number tickers, IntersectionObserver-triggered, reduced-motion safe)
- §5 **Segmented control** (radio-group pill, for period/mode toggles)
- §6 **Filter chip with close button** (dashboard active-filter row)
- §7 **Banner** (full-width inline notice, less prominent than `.alert`)
- §8 **Avatar** (image or colored initials, with byline composition)
- §9 **Stat-card grid** (composition pattern for dashboard rows)
- §10 **Reference table** — when to use which numeric display

### Added — `evals/evals.json`

New eval `longform-study-note-bayes`:
- Tests TOC + collapsible + quiz + footnotes simultaneously
- Verifies pedagogy rules from `pedagogy.md` are applied (layered explanation, concrete-before-abstract, misconception addressed)
- Verifies the chat reply ends with the verification trail line per `verification.md` §8
- Bridges the gap between visual-quality testing (existing 3 evals) and content-quality testing (which v1.0 didn't cover)

### Updated — routing tables

`SKILL.md` §1 now routes:
- Dashboard requests → `patterns-charts.md` + `patterns-data-display.md`
- Math/DS tutorials → `patterns-math.md` + `patterns-learning.md`
- Long-form study notes → new dedicated row pointing at `patterns-learning.md`

`SKILL.md` §6 file structure updated to list both new files.

### Not changed (intentional)

- `assets/starter-template.html` left as a minimal starter — does not include chart palettes (`--cat-*`, `--seq-*`, `--div-*`), `--font-math`, or full breakpoint tokens. This is intentional: starter is for quick prototypes; pull additional tokens from `tokens.md` when needed.
- All 5 immutable content rules (rules 11-15) untouched. They were already correct.
- `pedagogy.md`, `plot-quality.md`, `verification.md` content untouched.
