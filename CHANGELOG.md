# CHANGELOG — create-visual-explainer

> Older entries (v1.1–v1.4) live in `CHANGELOG-archive.md` to keep this file scannable. v1.5+ stays here as the active history.

## v1.15 (2026-07-14) — Editorial Bulletin skin + `usecases/analytical-report.md`

User direction: a warm-paper editorial design (FT / Economist / Stripe Press lineage — Space Grotesk + IBM Plex over `#F7F6F3`, muted palette, hairline rules) recurs whenever Claude free-hands a report/dashboard; codify it as a reusable **skin** instead of re-inventing bespoke tokens each time, and add a home for the *report* page-type — without letting it collide with the explainer usecases.

Two orthogonal axes made explicit: **skin** (`data-skin` — font + surface + palette *family*) is distinct from **theme** (`data-theme` — colours within a family) and from **palette** (`data-accent` — identity hue only). The editorial skin is a whole family; SaaS Polish stays the default skin dialed light. Three families is treated as the ceiling.

**New skin — `foundations/tokens.md` §5.5 `[data-skin="editorial"]`**: full token block (fonts, warm-paper surfaces with a `--rule-ink` baseline, WCAG-verified text, text-safe semantic accents with the brighter editorial hues living in the chart palette as fills, dusty-blue identity, categorical/sequential/diverging palettes), the editorial font `<link>`, and three conventions (rules-not-shadows, grid-texture-off, dark-ink-tags-not-white-on-solid). Every colour verified: text AAA/AA on `#F7F6F3`; the bright hues fail as body text by design and are confined to fills.

**New file — `usecases/analytical-report.md`**: the report page-type, bound to the content pillar so it stays a *readout*, not a bare dashboard. Core is the **discriminator** (learn-a-concept → explainer; read-results → report; glance-and-react → live dashboard) with tie-breakers (any teaching ⇒ explainer; unsure ⇒ default explainer; §2C as last resort), Load-when / Do-NOT-load-when, the rule 14 + rule 16 binding, and a generalized report anatomy.

### Wired in

- **rule 4** reworded: "one trio per skin" (default vs editorial), never Inter/Roboto/system-ui — the immutability is now per-skin.
- **§4** gains an **Aesthetic family** picker (register test: read-&-reason vs glance-&-react vs marketing) above the dense/polished dial; footer family line + siblings updated (adds Editorial Bulletin, FT, Economist, Stripe Press).
- **§1 skill map**: `tokens.md` row notes §5.5 skins; new `usecases/analytical-report.md` row with the discriminator; task-type table splits **live dashboard** (scan-and-react, dark) from **analytical report** (snapshot-to-read, editorial).
- **§3 workflow step 6**: pick **family/skin** first, then `data-accent` + `data-bg` within it (editorial ignores `data-bg`).
- **`foundations/palettes.md`**: note that a skin is *not* a palette — a family (fonts+surfaces) is a tokens-level `data-skin` override, not a `data-accent` entry; the two axes compose.
- **`MAP.md`**: usecases count 4→5, `analytical-report.md` added to tree + detail entry; tokens note updated for skins.

Net: **1 new skin block (`tokens.md` §5.5) + 1 new usecase file + rule 4 reworded + §4 family picker; no component rewrites (every component already reads the tokens).**

## v1.14 (2026-07-14) — Structure-first layout: add `foundations/structure.md` + rule 17

User direction: when building any learning page, Claude must actively derive the most logical, coherent macro-layout for the *specific* knowledge — and silently self-check it — instead of defaulting into a template or a task-type bucket. This version adds that missing layer as a new foundation and wires it into both the build path and the verify path.

**New file — `foundations/structure.md`** (the page-structure analogue of `foundations/plot-quality.md`): the "a layout can lie" principle, the name-the-shape → match-the-layout method, the full shape→layout table (8 shapes incl. cycle) with a "the lie if you mismatch" column, mixed/nested handling, and a silent §4 self-check (name test · match test · spine-as-a-sentence test). It lives in `foundations/` — not as a `pedagogy.md` subsection — on purpose: macro-layout is a construction-time *structural* concern (the twin of chart integrity), not a prose/explanation concern, so it belongs beside `plot-quality.md`, loads on every educational page, and reads as a build-time decision rather than a writing tip.

Verify is **silent self-check only** (user's explicit choice): no trail line, no ask-before-build, except when two shapes genuinely compete → §2C. Net: **1 new file (`foundations/structure.md`) wired into 5 surfaces + 1 new rule (17) + 1 new eval (8); `pedagogy.md` unchanged from v1.13.**

### Wired in — `foundations/structure.md`

- **§1 skill map**: new `foundations/` row — "derive the macro-layout from the *shape* of the knowledge … a mismatched layout is a *structural lie*", `Load when: Baseline for any educational page — before picking usecases`.
- **§1 loadouts**: added to the default learn-everything "Always" set and to the "DS learning page" row.
- **§1 default path**: **structure-first** named as the DEFAULT *first build move* (ahead of §2D disclosure and §2E tooling), so build-time primacy sits where the approach is chosen — not buried in an appended rule.
- **`MAP.md`**: added to the `foundations/` tree (count 6→7) and a full detail entry (§1–§4) alongside the other foundations files.

### Added — `SKILL.md` §2B rule 17 "Page structure mirrors the shape of the knowledge"

Seventh content rule (§2B now 7 rules, was 6). Page-level counterpart to rule 13's per-explanation form choice: name the shape → build the layout that expresses it. Framed as a *structural lie* — the layout-level analogue of a chart that misleads about its data (a truncated axis, a dual axis; `plot-quality.md` §3–§4); it is to page layout what rule 14 is to plots. Points to `foundations/structure.md`.

### Changed — `SKILL.md` workflow + anti-patterns

- §3 workflow: step 4 renamed "Plan structure, content + disclosure split" — diagnose the shape and derive the macro-layout *first* (rule 17 + `structure.md`), then apply rules 11-13 within it; step 9 self-test gains the silent `structure.md` §4 structure-match check (explicitly no line in the reply).
- Anti-patterns (content): a macro-layout that contradicts the knowledge's shape → structural lie.

### Changed — `foundations/verification.md`

- §1 pre-output checklist: new **Structure / layout** subsection carrying the silent self-check, pointing to `structure.md` (a process isn't laid out as an unordered set; a comparison isn't split into sequential essays; a hierarchy isn't flattened into a peer list).

### Fixes applied during finalization

- **`structure.md` Quick map**: corrected the §2/§3/§4 line ranges to `§2 L27–46 · §3 L47–55 · §4 L56–66` — the earlier ranges pointed past their sections, and §4's end (L78) ran 12 lines beyond the 66-line file.
- **rule 17 citation**: the *structural lie* example previously cited "(rule 14)" for a truncated axis, but rule 14 is the axis-*labels* rule; the truncated/dual-axis example now points to `plot-quality.md` §3–§4, with the rule-14 parallel kept correctly ("to page layout what rule 14 is to plots").
- **`evals/`**: added **eval 8** (`comparison-optimizers-facing-columns`, `positive-but-tests-rule-17`) — the page-level twin of eval 4; a comparison-shaped prompt described only by *intent* (no "make a table"), checking the output is a facing-column comparison surface rather than three sequential per-optimizer essays. Bumped `evals.json` to v1.4, renumbered its two `future_work` placeholders 8/9→9/10, and corrected the stale "all 6 evals" → "all 8".

## v1.13 (2026-07-10) — Reader-tooling layer: notes panel + attention support (ADHD/PDHD)

User direction: the skill had no guidance for the *reader-tooling* layer — the notes feature, and the attention-support affordances (dim non-active sections, per-section done checkbox, progress line) that a real dense page needs. A reference page (`research.html`) shipped all of them; nothing in the skill described how or why. Root cause: the skill covered *content* (pedagogy) and *depth layout* (progressive disclosure) but never *sustained attention* — the axis that decides whether a dense page gets finished at all. Net: **1 new file, 1 new section, rules added in 3 files; `evals/` untouched.**

### Added — `components/reader-tools.md` (new, ~578 lines)

The reader-controlled layer that sits on top of content. §1 Why (failure-mode → tool table, 3 laws) · §2 the `data-concept` contract (single join key for ticks / focus spy / notes / detail panel; composite `panelId::conceptId` when the page has two reading modes) · §3 top reading-progress bar · §4 per-concept done-tick + `~4 phút` reading time · §5 focus dock (`7/23 đã đọc` counter + jump-to-next-unread) · §6 focus mode (dim via `opacity`+`saturate`, **never `blur()`** — with the accommodation-strain argument) · §7 notes panel (left-edge launcher, auto-attach to the current concept, filter here/all, armed delete, `.md`/`.json` export, merge-import) · §8 persistence conventions (guarded `localStorage`, versioned keys, migrate-never-wipe) · §9 a11y/print/reduced-motion/script order · §10 anti-patterns.

### Added — `SKILL.md` §2E "Reader tooling — the default attention-support layer"

Third axis alongside rules 11-13 (content) and §2D (depth layout). Ships **by default** on any study page with ≥5 concepts; explicitly **not gated by §2C** — accessibility infrastructure isn't an optional extra. Three laws: reader-controlled never automatic (no auto-ticking on scroll) · additive never subtractive (dim, don't hide) · state local, guarded, exportable (`localStorage` **is unavailable in the claude.ai artifact sandbox** — an unguarded call kills every script below it).

### Added — `usecases/progressive-disclosure.md` §7 "Two-tier reading modes"

The simple/detail tab split that §2D already referenced but nothing documented: same `data-concept` in both panels, tab ① must be self-sufficient, disclosure still nests inside tab ② (a detail tab is not a licence to inline code), announce the split in the overview block, re-run panel-scoped scripts on switch, two tabs never three.

### Changed — `SKILL.md`

- §1 skill map: new `components/reader-tools.md` row; added to the default learn-everything loadout and the "DS learning page" loadout row.
- §2C: reader tooling added to **Required content** (≥5 concepts); `reading-progress bar` removed from the Optional list (now default, not an extra); new note that §2C doesn't gate this layer.
- §3 workflow: step 5 renamed "Plan navigation + reader tooling" — assign stable `data-concept` ids at planning time; step 9 self-test gains the §2E checks.
- Anti-patterns: `blur()` on body text (visual) · auto-ticking on scroll · focus-on-by-default / `display:none` on dimmed sections · unguarded `localStorage` · notes with no export / replace-import · reading-time computed over KaTeX+`<pre>` · progress bar as the only progress signal · a ≥5-concept page with no reader tooling (content).

### Changed — `MAP.md`

`components/` is now 7 files; new detail entry for `reader-tools.md`; §2D/§2E added to the SKILL.md section list; progressive-disclosure §7 recorded.

No file moved or removed; `evals/` preserved verbatim.

## v1.12 (2026-07-01) — Block-level disclosure, spine-per-reading-mode, print/no-JS reveal

User direction: a page built with a session-specific *simple/detail two-tab* split still showed code and supplementary boxes dumped inline inside the detail tab. The running model (and the skill text) only described disclosure at *concept* grain — brief → panel with a whole concept's depth — and read "main column" as the one-column briefs, so a *detail tab* looked like a legitimate place to inline code. Root cause (not a broken file): §2D/§4 never named (a) block-level disclosure, (b) that the "skimmable spine" is per reading-mode, (c) multiple triggers per concept, (d) a block-level "don't-gut" rule, or (e) print/no-JS handling of the hidden sources. Net: **0 new files, rules clarified in 2 files; `evals/` untouched.**

### Changed — `SKILL.md` §2D

- **Granularity paragraph.** A trigger may reveal a *whole concept's* depth **or** a *single heavy block* (code / supplementary box / reference table), several triggers sharing the one panel; reach for block-level when the depth tier is itself long (e.g. a simple/detail tab split or a long deep section).
- **"Skimmable spine" is per reading-mode.** The spine is whatever the reader scrolls by default *in the mode they're in* — the detail-mode body counts too; a "detail tab" is not a licence to inline code. Disclosure nests.
- **Self-sufficiency test** gains a block-level clause: never disclose a concept's *only* substantive content (empty heading + a button = hiding, not disclosure).

### Changed — `SKILL.md` anti-patterns

- Reworded the "dumping code inline" anti-pattern to say the spine is per reading-mode (even inside a detail tab, code stays one click away).
- New anti-pattern: disclosing a concept's *only* content.

### Changed — `usecases/progressive-disclosure.md`

- **§2 layering law:** added the **don't-gut rule** (block level).
- **§4:** new "Multiple triggers in one concept" note + example (each heavy block its own `data-expand` + `.detail-source`, sharing the one panel; stated the `data-expand="X"` → `#detail-X` uniqueness convention).
- **§5:** new **Print & no-JS** rule — reveal `.detail-source` inline and hide the panel/triggers in `@media print`, so a printout / no-JS render stays complete.
- Quick-map line ranges refreshed.

No file moved or removed; `evals/` preserved verbatim.

## v1.11 (2026-06-30) — Progressive disclosure promoted from opt-in to DEFAULT reading layout

User direction: a freshly generated page no longer used the brief/expand layout. Root cause (not a broken file): v1.10 imported `usecases/progressive-disclosure.md` correctly but wired it as a *conditional* ("dense pages, skim-then-dive") and never reconciled it with §2C, whose "Optional content" list flagged "going deeper" collapsibles as **opt-in (only if the user explicitly requests)**. So the running model suppressed the layout by default. Net: **0 new files, router + rules rewired so the layout is the default; `evals/` untouched.**

### Changed — disclosure is now the default, not gated

- **New `SKILL.md` §2D "Progressive disclosure — the default reading layout."** States the split explicitly: main column carries the full Layer 1+2 + key formula + essential teaching visual; code, supplementary diagrams, Layer-3 derivations, edge cases and extra detail are disclosed **by default** (no asking). Mechanism by size — multi-concept → right detail panel; single short concept → inline `<details>`. Carries the self-sufficiency test as a hard rule.
- **§2C reconciled.** Removed `"going deeper" collapsibles` from the Optional list; clarified `code playground` → `interactive code playground (editable/runnable sandbox)` (a static code listing in the panel is now default, not an extra). Added a note that §2C gates *whether an optional extra exists*, not *where committed depth lives*.
- **Per the chosen scope:** essential teaching visuals (spatial/temporal concepts) stay inline — rule 13 preserved; only *supplementary* diagrams are disclosed.
- **Rewired the router:** §1 skill-map row, the default learn-everything path, and the DS-learning loadout now name PD as the default for multi-concept (was "as needed" / "if the page is dense"). Added PD to the Required-content list. Workflow step 4 gains a "plan the brief/expand split" instruction; step 9 self-test gains the self-sufficiency check.
- **Three new anti-patterns:** dumping code/derivations/tables inline; hiding the core teaching visual behind a click; a brief that can't be read without opening its panel.
- **`usecases/progressive-disclosure.md`:** its §2 "Relationship to §2C" paragraph (which said the split was §2C-gated) rewritten to defer to §2D (split = default, extras = gated); §5 notes single-concept pages use the inline accordion as the *primary* mechanism, not just a mobile fallback.

No file moved or removed; `evals/` preserved verbatim.

## v1.10 (2026-06-30) — Visual variety (subject palettes + background textures) · brief/expand reading layout

User direction: the skill produced a near-identical template every run (always the green `--accent-green` identity, always the 40px square grid), which felt monotonous; and a reader-layout draft was ready to fold in. Net: **3 new files, 1 backward-compatible token hook, router wiring across 3 files.** No file moved or removed.

### Added — `usecases/progressive-disclosure.md` (imported from a user-authored draft)

Overview-first + brief/expand right detail panel: an advance organizer up top, each concept showing a *brief* (Layer 1–2 + the one key formula) inline with depth (Layer 3, worked numbers, code) one click away in a slide-in panel; includes drag-to-resize. The spatial form of rules 11 + 13. Cross-refs to `longform-study.md`, `components/asides.md`, `components/core.md`, `interactive-explainer.md` were all verified to resolve.

On import, its 8 `--accent-green` uses (expand button, panel resize handle, overview-map hover + index keys — all *identity* chrome, none semantic) were switched to `--accent-identity` so the page themes with the rest of the system. Wired into `SKILL.md` §1 usecases table, the default + DS-learning loadouts, and `MAP.md`.

### Added — `foundations/palettes.md` (the "color" half of a theme set)

Subject identity palettes (Toán/Lý/Hoá/Xác suất/Tự nhiên/Mật mã) selected via `data-accent` on `<html>`, mirroring `data-theme`. The governing decision, stated as §2's law: **identity changes per subject, semantics never do.** `--accent-green` (and its five siblings) carry two jobs — a *brand/identity* job (eyebrow, title keyword, focus ring, links, TOC active, progress bar) and a *semantic* job (success/danger/warning, chart `--cat`/`--seq`/`--div`, status chrome). Only the first is themed; recoloring the second would make a chemistry page where "success" and "danger" are both orange — no color language left. So theming routes through one new token and a curated wiring list, **not** a find-replace.

Selection is subject-first (infer from topic → preset), with a **curated random** fallback drawn only from the six WCAG-verified accents — never a random hex, which would fail contrast unpredictably and drift toward synthwave. Hues reuse verified accents wherever possible; the one new hue (chemistry orange) is contrast-checked in §6 (8.7:1 dark, 5.0:1 light).

### Added — `foundations/backgrounds.md` (the "background" half of a theme set)

Texture variants for the `body::before` signature layer — dots, crosshatch, contour, scanline (static) and drift, wave (animated) — plus a gridless identity-tinted `glow` and a `none` off-switch, selected via `data-bg`. Guardrails baked in: monochrome `--hairline` (≤0.06 alpha for the one tint), and animation restricted to `transform` with a `prefers-reduced-motion` off-switch (rule 9) — a continuously-animated SVG turbulence filter is called out as a perf/rule-9 footgun and declined for full-page use. Every variant *is* `body::before`, so the chart-container exclusion (`tokens.md` §1.6) and print-hide (`tokens.md` §5.4) cover them for free — no duplication.

### Changed — `foundations/tokens.md` (3 surgical hooks + 2 pointers, backward-compatible)

Introduced the identity token in §1.3 — `--accent-identity` / `--accent-identity-rgb`, both defaulting to `var(--accent-green)` — and pointed `.eyebrow` + `.title span` (§2.4) and `:focus-visible` (§6) at it. Added pointers from §1.3 → `palettes.md` and §1.6 → `backgrounds.md`. Because the default resolves to green, **every existing page renders byte-for-byte as before**; a page only changes when it opts into `data-accent`/`data-bg`.

Why edit tokens.md rather than self-contain in the new file: a token must be defined exactly once, and the header/focus CSS that consumes it already lives here — defining a themed header anywhere else would duplicate it, the exact failure mode v1.8 removed a whole file to avoid. The six semantic accents and all chart palettes were left untouched (verified: `--accent-identity` appears in exactly 5 spots; `--cat-1` etc. unchanged).

### Changed — `SKILL.md` (router wiring, no rule changes)

Foundations table +2 rows (palettes, backgrounds); usecases table +1 row (progressive-disclosure). The default learn-everything loadout and the DS-learning loadout now name the theme set and progressive-disclosure. §3 step 6 ("Pick starting point") folds in theme-set selection (subject-inferred default, user override wins) — this is where the "every page looks the same" complaint is fixed at the workflow level, without renumbering. §4 cheatsheet gains `--accent-identity`. §5 visual anti-patterns +2: don't recolor semantic accents to match a subject; no loud/synthwave animated background. The 16 immutable rules and §2C are unchanged.

### Changed — `MAP.md`

Directory tree + counts updated (foundations 4→6, usecases 3→4); detail sections added for the three new files in the existing Vai-trò / Load-khi / Quick-map format.

---

## v1.9 (2026-05-07) — Fourth skill-creator review · narrowed description · output channel rule · evals path migration

Skill-creator review of v1.8 + user direction to scope triggering tighter (caller-first invocation). 4 edits across 4 files.

### Changed — `SKILL.md` description narrowed to caller-first triggering

Old description listed broad Vietnamese/English keywords (`giải thích X`, `trang HTML`, `dashboard`, `how does X work`) that overlapped with everyday requests Claude can already handle. Caused over-triggering on generic web-page or "explain this concept in chat" tasks where the full skill machinery is overkill.

New description triggers on (a) explicit skill invocation by name (`create-visual-explainer`, `/create-visual-explainer`, `dùng skill visual-explainer`), or (b) clearly-named educational deliverables (`tài liệu học`, `note bài giảng`, `study note`, `math note`, `interactive explainer`). Generic `giải thích X` / `tạo trang HTML` / `làm dashboard` no longer trigger absent explicit invocation. Negative list extended.

### Added — `SKILL.md` §3 step 7 "Pick the output channel"

Skill produces longform HTML (multi-section, TOC/sliders/charts) but never told Claude where to put it. New rule: default to an **HTML artifact** so the user can view/download/edit. Inline visualizer (`visualize:show_widget`) reserved for small ≤300-line single-concept pages. Never embed as a chat code block. Phrasing kept portable across Claude environments — no hardcoded sandbox paths.

Workflow renumbered: old steps 7–8 are now 8–9, with new step 10 for the verification trail handoff (was step 9).

### Fixed — `evals/evals.json` reference paths migrated to current 3-bucket layout

v1.5 refactor moved `references/*.md` flat layout → `foundations/`, `components/`, `usecases/`. Eval file was missed in that pass. 27 occurrences updated across all 4 positive evals. Bumped eval schema version 1.1 → 1.2. Affects only loadout-check assertions, which were silently false-failing in any auto-grading run.

### Fixed — `evals/README.md` removed dangling pointer to non-existent `scripts/check_assertions.py`

Note now says to run auto-checks manually with shell tools.

---

## v1.8 (2026-05-05) — Third skill-creator review · remove preflight wrapper · readability polish · evals in bundle

Third skill-creator review of v1.7 surfaced 1 architectural decision (preflight wrapper not pulling weight) + 6 readability/consistency gaps. All actioned. Net change: 1 file removed, 7 edits across 2 files, 1 build-pipeline change, ~30 lines net in SKILL.md.

### Removed — `references/preflight-checklist.md` (reverses v1.7 P2 addition)

Investigation started from a cross-ref bug — rule 13 in preflight pointed to `pedagogy.md §5` ("The cognitive load budget") when the actual visual-vs-prose logic lives in `§7` ("Visuals: when they help, when they hurt"). One-line fix would have been `§5 → §7`.

But the bug surfaced a deeper finding: **preflight-checklist.md is 100% duplicate content** of self-test sections that already exist in the source files. Worse, it was already drifting — `pedagogy.md §9` has 8 checkboxes + a "19-year-old test" meta-check, but preflight §1 had only 6 checkboxes (missing reader-ahead, cut, end, 19-year-old). At creation time it was already a partial copy; future drift was guaranteed.

The original rationale for preflight (per v1.7) was "agent had to load 3 files to walk one checklist" — but workflow §3 steps 3–7 already require loading `pedagogy.md` + `verification.md` + `plot-quality.md` for the build phase. By the time step 8 (self-test) runs, the 3 files are in context. Preflight saved zero loads; it only saved "remembering which section in which file" — at the cost of permanent maintainer-sync overhead and source-of-truth ambiguity.

Replaced workflow §3 step 8 with direct pointers: `pedagogy.md §9 → plot-quality.md §2 → verification.md §1 + §8`. Removed the row from §1 Skill map. The original cross-ref bug becomes moot — the file holding it no longer exists.

### Fixed — rule 13 missing section pointer in `SKILL.md` §2B (P0)

All other content rules (11, 12, 14, 15, 16) ended with `(See pedagogy.md §X.)` or equivalent. Rule 13 had only the lineage cite. Reader who wanted to dig deeper had no pointer to authoritative source. Fixed: rule 13 now ends with `(See pedagogy.md §7. Lineage: Feynman, Ciechanowski, 3Blue1Brown.)`.

### Trimmed — rule 13 prose for rhythm consistency (P1)

Rule 13 had 4 emphasis styles (bold, italic, parenthetical, asterisks) where rules 11/12/14/15/16 follow a clean "**Bold lead.** Plain explanation. (See X.)" template. Reader processing rules with rhythm hit a speed-bump at 13. Trimmed:
- Removed "Words are right when the concept is fundamentally linguistic or definitional" — implied by the inverse of "Visuals are mandatory only when... structure prose can't carry".
- Removed "Reader's understanding is the metric, not the artifact's beauty" — implied by "Pick by load on working memory, not by aesthetic".
- Reduced italic emphasis on "*fastest path to understanding*" and "*load on the reader's working memory*" — bold lead carries the weight, body should read flat.

Net: 5 sentences → 3 sentences. Same content; lower cognitive load.

### Reordered — §3 and §4 swapped (P2)

Old order interrupted process flow:
> §1 Map → §2 Rules → §2C Ask → §3 Cheatsheet → §4 Workflow → §5 Anti-patterns

§3 cheatsheet was a *resource*, sitting between *rules* (§2C) and *process* (§4 workflow). Reader had to context-switch out of "what to do" into "what tokens exist" then back into "how to do it".

New order keeps process contiguous:
> §1 Map → §2 Rules → §2C Ask → §3 Workflow → §4 Cheatsheet → §5 Anti-patterns

§3 workflow now follows §2C clarification (process flows: ask → execute), §4 cheatsheet sits as a reference resource right before anti-patterns. Updated 1 cross-reference (`§3 cheatsheet directly` → `§4 cheatsheet directly` in §1 loadout table).

### Reformatted — first row of loadouts table extracted to callout (P2)

The "Default learn-everything path" row was ~200 chars with nested `(X if A, Y if B, Z if C)` logic, dwarfing the other 6 rows (all <100 chars). Hard to read on narrow screens; the default path also deserves visual prominence over specialized loadouts. Pulled it out into a callout with two bullets ("Always:" / "Add as needed:") above the table; table now contains only the 6 specialized rows. Same content; lower density.

### Clarified — workflow §3 step 6 splits fresh vs editing (P3)

Old step 6 read `Start from assets/starter-template.html if fresh page; read visual references based on what's on the page` — the "if fresh" condition was clear, but "what if not fresh?" was implied rather than stated. Reader scanning could mistake step 6 as fresh-only and skip when editing. Rewritten as two explicit branches: `Fresh page → start from template. Editing existing page → skip template; read visual references for any element already present.`

### Reframed — rule 8 explicit about being mirror of rule 1 (P3)

Old rule 8: `Triple-opacity for state colors too. Success/warning/danger/info follow the same pattern.` — read like padding to reach "10 visual rules". New: `Triple-opacity for state colors too (mirror of rule 1). Success/warning/danger/info follow the same solid / 0.08–0.10 / 0.20–0.25 pattern.` Same length, but the relationship is now visible without cross-referencing rule 1 mentally, AND the concrete opacity values are echoed inline.

### Archived — older CHANGELOG entries (P3)

Moved v1.1–v1.4 (the early-evolution, pre-reframe period) to `CHANGELOG-archive.md`. Active CHANGELOG now starts at v1.5 (the learn-everything reframe), keeping ~16K visible vs ~28K. Archived entries are still reachable for historical context, just not on the default reading path.

### Build — `evals/` now included in `.skill` bundle (deviation from Anthropic default)

Anthropic's `package_skill.py` excludes `evals/` at the skill root by default (`ROOT_EXCLUDE_DIRS = {"evals"}`, line 24) — rationale: evals are maintainer regression tests, not runtime resources Claude loads when the skill triggers. For this skill (private/personal use, not public distribution), the user prefers `.skill` to be self-contained: regression tests travel with the skill so a future re-install / move retains the test set.

For this build: copied `package_skill.py` to writable location, removed `"evals"` from `ROOT_EXCLUDE_DIRS`, re-ran. The `.skill` bundle now contains `evals/evals.json` + `evals/README.md`. To reproduce on a future rebuild: either repeat the local-edit recipe, or use `cd /tmp && zip -r create-visual-explainer.skill create-visual-explainer/` (`.skill` files are ZIP archives with a renamed extension — `file create-visual-explainer.skill` confirms).

### Not changed

- **Anti-patterns §5** — kept duplicating rules from §2 (decision per v1.6: user prefers redundancy for recall).
- **Reference files** — except for the deletion of `preflight-checklist.md`, no content edits to remaining 13 files. Source-of-truth files (`pedagogy.md`, `verification.md`, `plot-quality.md`) had the self-test content all along.
- **Evals content** — `evals.json` itself unchanged (still 6 prompts, 4 positive + 2 negative-trigger). Inclusion in `.skill` bundle is a packaging change, not a content change. Re-running 6 evals would still be useful to verify rule 13 trim doesn't hurt eval id=4 (prose-first stress test); punted to next iteration.
- **Loadout coverage in §1 table** — same files, same trigger conditions; only the *visual layout* of the table changed (and the preflight row was removed).

---


## v1.7 (2026-05-05) — Add evals · preflight checklist · TOC for 2 large refs

Second skill-creator review (post-v1.6) surfaced 4 gaps. All actioned.

### Added — `evals/` folder (P0 from review)

The skill previously had no test set, meaning every edit risked silent regression. Added 6 test prompts in `evals/evals.json`:

- 4 positive evals covering the main loadouts: interactive-explainer, dashboard, longform-study-note, prose-first (rule 13 stress test).
- 2 negative-trigger evals (Swift bug fix, decline email) to catch description over-broadness.

Each eval has `success_criteria` and `failure_criteria` with `auto: true` flags marking which can be checked by script vs need human review. Stopping condition: ≥80% success per eval, zero failures, zero negative-triggers loading the skill.

User had previously drafted `evals.json` (uploaded May 5) under the old skill name `industrial-data-design`. That draft contained an `ios-model-details-screen` test, which actually belonged to sister skill `industrial-data-ios` — `SKILL.md` explicitly says "Do NOT use for iOS UI". That eval was removed from this skill's evals (noted in `evals.json` `future_work` for migration to the iOS skill).

### Added — `references/preflight-checklist.md` (P2 from review)

Workflow §4 step 8 previously said "self-test per pedagogy.md §9" but the actual self-test was scattered across `pedagogy.md` §9, `plot-quality.md` §2, `verification.md` §1+§8 — agent had to load 3 files to walk one checklist. New consolidated file reads them all in 5 sections (pedagogy, plots, math/code, handoff, visual) with each item linking back to the authoritative source. Source files unchanged — no DRY violation; this file only collects, doesn't redefine.

Workflow §4 step 8 in `SKILL.md` updated to load this file instead.

### Added — Quick map to 2 reference files (P1 from review)

`patterns-charts.md` (373 lines) and `patterns-math.md` (326 lines) were the only refs >300 lines without a Quick map at the top, violating the Anthropic skill-creator rule "For large reference files (>300 lines), include a table of contents". Other 11/13 refs already had Quick map. Fixed both — Quick map content matches the actual `## N.` headings present in each file (verified by grep).

### Skipped — `assets/starter-template.html` TODO comments (P3 from review)

Considered adding `<!-- TODO -->` / `<!-- KEEP -->` / `<!-- DELETE -->` comments to clarify which blocks are rule-bearing vs placeholder. Skipped for now — agent can infer from context, and adding 30+ comments risks cluttering the boilerplate. Reconsider if eval results show agent removing rule-bearing blocks.

### Skill map updated

`references/preflight-checklist.md` added as a new row. Loadout table not changed (preflight is loaded at workflow step 8, not as part of initial loadout — it's a final-check file, not a content-generation file).

---

## v1.6 (2026-05-05) — Rename · trim description · compress workflow

Skill-creator review of v1.5 surfaced 3 minor improvements. Action #1 (split into sister skill `industrial-data-app-shell`) was considered then **rejected** — user confirmed dashboard/marketing modes are still occasionally needed and splitting would force shared resources (tokens, components, charts) into duplicate-or-symlink — net complexity rather than simplification. Remaining 3 actions executed.

### Renamed — skill identifier

`industrial-data-design` → `create-visual-explainer`. Old name carried legacy bias toward dashboard/data-viz aesthetic (the v1.4 framing); new name reflects the v1.5 reframe to learn-everything visualization. The "create-" prefix matches the user's mental model (skills as creation tools), and "visual-explainer" is the umbrella term that covers all primary use cases — study notes, math/DS tutorials, interactive concept explainers, longform tutorials. Aesthetic family name "Industrial Data" remains in the H1 subtitle and footer as a stylistic descriptor (lineage cite), separate from the skill identifier.

Updated: YAML `name:`, H1 title, CHANGELOG title, `assets/starter-template.html` `<title>` tag. Sister skill `industrial-data-ios` reference unchanged (still points to existing iOS skill).

### Trimmed — frontmatter description (~140 → ~110 words)

Removed two pieces of content that were not contributing to triggering accuracy:

- **`Uses Bebas Neue + DM Sans + JetBrains Mono.`** — implementation detail. Claude does not select skills based on font choices; this belongs in body §3 (token cheatsheet) where the fonts are already declared.
- **`Secondary modes — dashboards (ClickHouse / Sentry / Grafana density) and marketing (GitHub / Tailwind / Notion polish).`** — the trigger keyword `"dashboard"` already appears in the explicit trigger list later in the description, so removing the elaborate parenthetical does not lose triggering coverage. The capability remains (files `patterns-navigation.md` and `patterns-dashboard-chrome.md` still ship in references), just no longer marketed in the description.

Added Vietnamese trigger keywords matching user vocabulary: `"tài liệu học"`, `"ghi chú"`, `"note bài giảng"`. Previously only `"giải thích X"` and `"trang HTML"` were Vietnamese; user actually uses these other phrases more often for study-note requests.

Promoted lineage cite to standalone sentence (`Pedagogy lineage: Distill / 3Blue1Brown / Brilliant / Ciechanowski.`) — was previously buried in a parenthetical at the end of the primary use case description.

### Compressed — workflow §4 (15 → 9 steps)

Old workflow restated content from rules in §2 inline, creating duplication: step 10 paraphrased rule 14 (plot quality), step 11 paraphrased rule 15 (verification), step 12-13 overlapped each other and rule 15-16. New workflow points to rules + reference files instead of restating their content.

Specific merges:
- Old steps 4 + 5 + 6 (rule 13 + Layer 1→2→3 + misconception) → new step 4 ("plan content" — rules 11-13 in one)
- Old steps 7 + 9 (read visual refs + start from template) → new step 6
- Old steps 10 + 11 (plot checklist + verification checklist) → new step 7 (apply quality checklists)
- Old steps 12 + 13 + 14 (self-test + verify + no unsolicited) → new step 8

Behavior preserved end-to-end. Side benefit: when a rule changes (e.g., rule 14 gets a new requirement), only §2 needs editing — workflow §4 inherits via reference.

### Not changed

- **Anti-patterns §5**: kept as-is. 5 of the 23 bullets paraphrase rules from §2 (specifically rules 11, 12, 14, 15, 16). Considered trimming for tinier SKILL.md, but user prefers readability over deduplication — the negative-framing reinforces recall and lets readers spot violations without jumping back to §2.
- **`patterns-navigation.md` (580 lines)** + **`patterns-dashboard-chrome.md` (260 lines)**: kept in skill. User confirmed dashboard/admin-panel use case may arise occasionally; splitting into sister skill would require shared-resource hacks (duplicate tokens.md/components.md/charts, or symlink, or 2-skill trigger) — none of which is simpler than 1 skill with all files local.
- **Reference files**: 12 files unchanged. No splits, no renames, no content edits.
- **Sister skill** `industrial-data-ios`: unchanged.

### Net effect

| Metric | v1.5 | v1.6 | Δ |
|---|---|---|---|
| Skill name length | 23 chars | 23 chars | 0 |
| Description word count | ~140 | ~110 | -22% |
| SKILL.md line count | 261 | 254 | -7 |
| Workflow §4 step count | 15 | 9 | -40% |
| Vietnamese trigger keywords | 2 | 5 | +3 |
| Reference files | 12 | 12 | 0 |
| Total skill line count | 5,567 | ~5,560 | ≈0 |

Net behavior is unchanged on existing workflows; description triggering should be slightly more precise on Vietnamese study-note requests; workflow is easier to maintain (rules are single source of truth).

---

## v1.5 (2026-05-05) — Reframe for learn-everything · single-purpose split · iOS spinout

Skill-creator review of v1.4 (re-purposed for **learn-everything visualization**) surfaced four issues. All four addressed in this version. Behavior on existing learning-content workflows is preserved or strengthened; iOS UI now lives in a separate sister skill.

### Reframed — frontmatter description

Description previously led with industrial-dashboard aesthetic ("dark-first, data-dense, ClickHouse / Sentry / Grafana / PostHog lineage..."). For a user whose primary use case is study notes, this primed the wrong aesthetic on every conversation. v1.5 leads with "learn-everything visualization" and cites the Distill / 3Blue1Brown / Brilliant.org / Ciechanowski lineage explicitly. Dashboard and marketing remain as named secondary modes. iOS triggers removed and pointed at sister skill.

### Added — Rule 13 (comprehension-first form) + renumbering

Rule 13 was previously "Every plot has axis labels..." That rule still exists (now renumbered 14). New rule 13 codifies the philosophy the prior version was missing:

> **Comprehension-first form.** Choose whichever explanation form gives the reader the *fastest path to understanding* — analogy, story, diagram, plot, worked number, table, code, interactive demo. Don't pick by aesthetic; pick by *load on the reader's working memory*. Visuals are mandatory only when the concept has spatial / temporal / quantitative structure that prose can't carry. Words are right when the concept is fundamentally linguistic or definitional. Reader's understanding is the metric, not the artifact's beauty.

Lineage credited: Feynman, Ciechanowski, 3Blue1Brown. Two new content anti-patterns added in §5 catching both failure modes (forcing a visual when prose is clearer; forcing prose when structure demands a visual).

Content rules: 5 → 6. Old rules 13/14/15 renumber to 14/15/16. All previous behavior preserved.

### Narrowed — Rule 16 (was rule 15) verification trail

Old rule 15 said "End every chat reply with a verification trail line." This fired even on trivial Q&A ("which color?") — heavy-handed and a yellow flag per skill-creator philosophy ("explain the why instead of rigid MUSTs"). User's actual preference is verification on **deliverables** (`tài liệu`), not every reply.

Rule 16 now scoped to deliverable handoffs (page, doc, code, math derivation, dataset, file). Pure conversational replies don't trigger the trail. Workflow §15 and §2C Required-content updated to match.

### Split — single-purpose principle

User criteria: "mỗi file có duy nhất 1 mục đích thôi." Three files were doing multiple jobs. After review:

**`patterns-learning.md` (642 lines) → split into 2:**
- `patterns-longform-nav.md` (~210 lines) — TOC sticky right-rail, reading-progress bar, section anchor links
- `patterns-asides.md` (~430 lines) — accordion/collapsible, sidenote (Tufte), footnote, quiz, vocab tooltip, pedagogy tie-in

Rationale: navigation chrome and aside content are distinct purposes. A page might need TOC without quizzes, or vice versa.

**`patterns-data-display.md` (559 lines) → split into 2:**
- `patterns-numeric.md` (~280 lines) — progress bar, meter, sparkline, count-up, stat-card grid, numeric selection guide
- `patterns-dashboard-chrome.md` (~210 lines) — segmented control, filter chip, banner, avatar

Rationale: numeric primitives serve both dashboards and learning-progress UIs; dashboard chrome is dashboard-only.

**Files reviewed and kept whole** (already single-purpose): `tokens.md`, `components.md`, `patterns-charts.md`, `patterns-explainer.md`, `patterns-math.md`, `patterns-navigation.md`, `pedagogy.md`, `plot-quality.md`, `verification.md`. `components.md` (672 lines) is large but all UI-primitive — splitting would add load-decision overhead without payoff.

### Spun out — iOS to sister skill

`platform-ios.md` (494 lines) was dead weight for the learn-everything-visualization use case. User confirmed preference to spin out. Now a standalone skill `industrial-data-ios` containing the same Theme.swift, AppFont/Spacing/Radius constants, SwiftUI/UIKit components, and Swift Charts integration.

iOS row removed from §1 loadout table and replaced with a pointer to the sister skill in the description and footer. Same token vocabulary across both skills — choosing which skill is just choosing target platform.

### Added — Default learn-everything path in loadout table

§1 loadout table previously had two separate rows for learning content ("DS learning page" and "Interactive concept explainer") — Claude had to mental-pick. New top row "Default learn-everything path" is the fast-path for the common "explain X / how does Y work" request, with conditional file loads based on what the topic actually has (equations, plots, interactive, longform, asides). The two specialized rows kept as fallbacks.

### Updated — `pedagogy.md` and `verification.md` flagged as baseline

§1 skill map now explicitly marks `pedagogy.md` as "**Baseline** for any educational content" and `verification.md` as "**Mandatory** for any math/code/factual claim in a deliverable." Previously they sat at the same indentation as 10 other reference files.

### Net effect

- Reference files: 13 → 12 (in this skill); +1 spinoff skill (industrial-data-ios)
- SKILL.md: 249 → ~280 lines (within 500-line budget)
- Bytes in this skill: ~252KB → ~225KB (iOS removed, but new files balance)
- Description: ~115 → ~165 words (added learning-first framing + sister-skill pointer)
- Routing speed: faster — default path is now top of loadout table
- Behavior: preserved on every existing rule; rule 13 (new) and rule 16 (narrowed) are the only behavioral deltas

### Not changed

- All visual rules (1-10) verbatim
- Content rules 11, 12, 14 (was 13), 15 (was 14) verbatim
- Cross-references in `pedagogy.md`, `verification.md`, `plot-quality.md`, `patterns-math.md`, `patterns-explainer.md`, `patterns-charts.md`, `patterns-navigation.md`, `tokens.md`, `components.md` — none pointed at the split files; no fixes needed
- `assets/starter-template.html`
- §2C clarification protocol behavior

---

