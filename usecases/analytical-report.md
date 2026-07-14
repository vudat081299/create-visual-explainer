# Analytical Report — Findings Readout

A recipe for a **snapshot analytical report**: KPI header, charts each carrying a stated finding, a written analysis, one source-of-truth footer. Think cycle-time report, cohort analysis, quarterly readout, research result summary, incident/postmortem, investor update. The reader comes to *read results about something they already understand* — not to learn a new concept.

This usecase exists to keep a report inside the skill's **content pillar** (findings, plot integrity, verification), so it stays honest and readable rather than becoming a generic "dashboard kit". It pairs naturally with the **editorial** skin (`foundations/tokens.md` §5.5) but doesn't require it.

**Quick map**: §1 The discriminator — report vs explainer vs live dashboard (L15) · §2 Load when / Do NOT load when (L37) · §3 Content-pillar binding (why this isn't a bare dashboard) (L61) · §4 Report anatomy (L87) · §5 Editorial pairing + tag caveat (L123)

---

## 1. The discriminator — report vs explainer vs live dashboard

This is the whole reason the file exists: a report and an explainer both "have charts and numbers", so without a hard test the router can't tell them apart and guesses. The test has a two-way answer, not a vague overlap:

> **Does the reader come to LEARN a concept they don't yet have (→ explainer / study), or to READ RESULTS about a domain they already understand (→ this file)?**

- **Explainer / study note** — has Layer 1→2→3 (rule 11), an "aha" moment, a worked example that *teaches* the mechanism; the reader is a newcomer to the idea. Route to `usecases/interactive-explainer` / `math-learning` / `longform-study`.
- **Analytical report (this file)** — the reader already knows what the metric *means*; they're here for *this instance's* numbers, trend, anomalies, and the analyst's read. It teaches no new concept. (The source cycle-time dashboard never explains "what cycle time is" — it shows a team's numbers to people who already know.)
- **Live dashboard** — real-time, alert-driven, glance-and-react (ops monitoring). That's a *dashboard-chrome* build on the **default dark** skin, not this. See the family picker in SKILL.md §4.

**Tie-breakers.** (a) If the page *both* presents results *and* teaches the concept ("explanatory data journalism"), it routes to **explainer** — any genuine teaching makes it an explainer; a report is defined by the *absence* of concept-teaching. (b) When you're unsure between report and explainer, **default to explainer** — teaching is the skill's centre of purpose, report is the periphery (and the §2C clarification protocol is the last resort: ask, don't guess). (c) Snapshot to *read* → this file; surface to *react to* → live dashboard.

---

## 2. Load when / Do NOT load when

**Load `usecases/analytical-report` when:**

- The deliverable is a **standalone results readout** — quarterly/monthly report, metric deep-dive, A/B or experiment result, cohort/funnel analysis, research findings summary, postmortem / incident report, investor or business update.
- The reader **already understands the domain** and wants *this instance's* data, trend, and interpretation.
- The page is a **snapshot** (a point in time or a saved query rerun), meant to be read and reasoned about — not a live monitor.

**Do NOT load it when:**

- The page **teaches a concept** the reader doesn't have yet → that's an explainer (`interactive-explainer` / `math-learning` / `longform-study`), even if it's chart-heavy.
- The surface is **live / real-time / alert-driven** → that's a dashboard-chrome build on the default dark skin (glance-and-react), not a report-to-read.
- It's a **single stat or a trivial number** → just answer inline; no usecase needed.
- The request is **marketing / a landing page** → `components/core` on the default skin, polished dial.

**Companion files** when this one is loaded: `components/charts` + `foundations/plot-quality` (every chart), `components/numeric` (KPI / stat-card grid), `foundations/verification` (rule 16 trail), and usually `data-skin="editorial"` (register). `components/dashboard-chrome` only if the report has real filters/segmented controls.

---

## 3. Content-pillar binding — why this isn't a bare dashboard

A dashboard shows numbers. A *report in this skill* is bound to the content rules, and that binding is the difference between a readout and decoration:

1. **Every chart states a finding (rule 14).** No chart ships as bare decoration: each has axis labels with units, a title/annotation, and at least one written takeaway. A report's charts are an *argument*, not a wall of panels. (`foundations/plot-quality.md` §2.)
2. **Plot integrity is non-negotiable (rule 14).** No truncated bar-chart y-axis, no dual y-axis implying false correlation — a report that distorts its own magnitudes is worse than no report. (`foundations/plot-quality.md` §3–§4.)
3. **A written findings section, in the analyst's own words.** State the trend, the exceptions, the caveats, the recommended next step — the interpretation is the point, the charts are evidence. (The source did this well: a "Nhận xét chính" block plus a flagged data-quality anomaly.)
4. **Flag data-quality caveats honestly.** If some records look wrong (the source flagged 10.4% of stories at cycle-time = 0 as likely bulk-transition errors), say so and exclude/annotate them. Silent inclusion of junk data is a form of lying with statistics.
5. **End with a verification trail (rule 16).** Report handoffs are exactly where this matters: state the data source + query, the as-of date, what was checked, what was excluded, and what remains uncertain. Never claim a check you didn't run.

Framed this way the report isn't scope creep — it's the **content pillar applied to a results page**. What the skill already had (`components/dashboard-chrome`) is UI parts; what it lacked is the discipline that keeps a report truthful and readable.

---

## 4. Report anatomy

A conventional, skimmable top-to-bottom order (generalized from the source dashboard). Structure still follows the knowledge's shape (rule 17) — most reports are *one situation examined from several angles*, so a single column with sectioned angles is usually right; use `foundations/structure.md` if the shape differs.

1. **Header** — eyebrow (source / report type) → title → a right-aligned `meta` block with the reporting period and data source. Uses the standard `page-head` (`tokens.md` §2.4); under editorial, a `2px solid var(--rule-ink)` sits under it.
2. **Method line (optional, one row).** A single bordered line stating the definition/formula the whole report rests on (e.g. how the metric is computed). This is the report analogue of "define before you show numbers" — it's not concept-teaching, it's pinning down *this* report's measurement.
3. **KPI / stat-card grid** — the headline numbers (total, mean, median, percentiles, an anomaly count). Use `components/numeric` stat-card grid; tabular numerals (rule 3). Mark a warning cell with the semantic danger accent, not a new colour.
4. **Anomaly / callout banner (if any)** — a single dashed-border banner surfacing the most important caveat, tied to the danger accent. See `components/dashboard-chrome` banner.
5. **Sectioned charts, each with its finding** — trend over time, breakdown by segment, distribution, top-N table. Each is a `.chart-container` (`components/charts`), obeys `foundations/plot-quality.md`, and is followed (or led) by its written takeaway (rule 14). A right-aligned data table for the top-N cases works well.
6. **Findings block** — the written analysis: trend, per-segment differences, data-quality note, suggested next step.
7. **Source footer** — the single source of truth: exact query/JQL/SQL, as-of date, and an explicit "snapshot, not auto-updating" note if true. This *is* part of the rule-16 trail, surfaced for the reader.

---

## 5. Editorial pairing + tag caveat

Reports are the home turf of the **editorial** skin — set `data-skin="editorial"` (`tokens.md` §5.5) for the paper/authoritative register. Two things to carry over:

- **Charts read from the palette, never hardcoded hex.** The source dashboard hand-rolled SVG with literal `#3D5A80` / `#5B6472` / `#EAE8E2`. In-skill, chart builders read `--cat-*`, `--text-muted`, `--border` so a skin swap re-themes every chart for free. If you lift the source's SVG helpers, replace the literals with the tokens first.
- **Category tags: dark ink on a tinted chip, not white on solid.** Only dusty-blue (`--cat-1`) and brick (`--cat-4`) pass WCAG with white label text; teal / amber / purple / slate as solid tag backgrounds fail for their label. Default tags to the triple-opacity chip (solid-colour text / 0.08 bg / 0.22 border). This is a correctness fix over the source, which used white-on-solid for every team colour.
