# Structure — deriving the layout from the shape of the knowledge

Rules 11–13 decide how to explain *one* concept. This file decides the **macro-layout of the whole page**: the order of sections, what sits beside what, what the spine narrates. The clearest page is the one whose *shape* matches the *shape of the knowledge it carries* — a process laid out as ordered steps, a comparison laid out as facing columns, a hierarchy laid out as nested sections.

Read this for **any educational page, before picking usecases**. It is the page-structure analogue of `foundations/plot-quality.md`: that file stops a chart from lying about data; this one stops a layout from lying about how the parts of an idea relate.

This is the **first construction decision**, made while you are still deciding what the page *is* — before form choice (rules 11–13), before the disclosure split (§2D), before any token. It is a shift in how you *build*, not a box ticked at the end: get the structure wrong and no amount of good form, clean disclosure, or careful verification rescues it, because the parts are already standing in the wrong relationship to each other.

**Quick map**: §1 The principle — a layout can lie (L11–26) · §2 Name the shape, then match it (L27–46) · §3 Mixed & nested shapes (L47–55) · §4 Self-check before styling (L56–66)

---

## 1. The principle — a layout can lie

A layout is not neutral packaging. Its arrangement makes a *claim* about how the parts relate, and the reader reads that claim before they read a word:

- Things in a numbered column → "these happen in this order."
- Things in facing columns → "compare these across the same rows."
- Things nested under a heading → "these are kinds of that."
- Boxes joined by arrows → "this one leads to that one."

When the arrangement's claim contradicts the knowledge's actual structure, the page misinforms *before the prose can correct it* — the reader has to fight the layout to understand. This is a **structural lie**, the layout-level twin of a truncated y-axis (lies about magnitude) or a dual axis (implies a correlation that isn't there). Two of the most common:

- A **process** shown as parallel cards → tells the reader the steps have no order, when order is the whole point.
- A **comparison** shown as sequential prose sections → forces the reader to hold option A in memory while reading option B, and hides the dimensions they should be comparing across.

So layout is a correctness concern, not a taste concern. **Derive it from the content; don't default into it.**

## 2. Name the shape, then match it

Two-step method, every page:

1. **Name the dominant relationship** between the page's parts in one phrase — *is this a sequence, a comparison, a hierarchy, a causal chain, one deep idea, a set of parallel items, a decision, a cycle?* If you can't name it, the content isn't organized yet — organize it before you style it (§4).
2. **Pick the macro-layout that expresses that shape most plainly.** The table below is a *starting point for the reasoning*, not a lookup you apply without looking — real knowledge is messier than eight rows, and most pages mix shapes (§3). Reason from the actual content first; use the table to check your instinct.

| Shape of the knowledge | Layout that expresses it | The lie if you mismatch |
|---|---|---|
| **Sequence / process / pipeline / timeline** (parts have a mandated order) | Ordered/numbered steps or a timeline; spine follows the steps | parallel cards → "order doesn't matter" |
| **Comparison / trade-off / before–after** (the point is the contrast) | Facing columns or a comparison panel; rows = attributes, eye moves across | sequential sections per option → reader compares from memory |
| **Hierarchy / taxonomy / part–whole** (nesting) | Nested sections parent→children, or an outline/tree; indentation = depth | flat list → the levels collapse |
| **Causal chain / dependency / mechanism** (X→Y→Z) | Flow diagram as the spine, then unpack each node; arrows carry "leads to" | bullet list → direction & linkage lost |
| **One deep idea, unfolded** (a single concept in layers) | Single-column progressive depth: Layer 1→2→3 down the page, depth disclosed on demand (§2D default) | many sibling sections → fakes breadth that isn't there |
| **Many parallel, independent concepts** (a survey/reference, no inherent order) | Index or grid + one section each, TOC-driven, jump in any order | forced linear narrative → implies a progression that doesn't exist |
| **Decision / "which should I use"** (branching conditions) | Decision tree / flowchart, or an "if you need X → Y" chooser | prose paragraphs → reader can't trace their own path |
| **Cycle / feedback loop** (the end feeds the start) | Circular / loop diagram | straight stepped line that visually "ends" → hides the loop-back |

The layout *mechanisms* live elsewhere — TOC & anchors in `usecases/longform-study`, the brief/detail split in `usecases/progressive-disclosure`, diagrams via `components/charts` or an inline SVG. This file only decides *which* shape to reach for.

## 3. Mixed & nested shapes

Real pages combine shapes: a comparison *of two processes*, a taxonomy whose every leaf is a mechanism, a sequence with one branching decision partway. Don't force the whole page into one archetype. Instead:

- Pick the shape of the **dominant** relationship for the **page spine**.
- Let **sub-shapes govern their local sections** — a comparison panel inside step 3 of a process is fine; a small flow diagram inside one row of a taxonomy is fine.

Nesting is expected. The test is only that *each surface* expresses *its own* level's relationship truthfully.

## 4. Self-check before styling

Silent pre-ship check — run it in your head, no output needed unless it fails:

- [ ] **Name test.** Can you state the page's dominant shape in one phrase? If not → the content isn't organized; reorganize before styling.
- [ ] **Match test.** Does the macro-layout express that shape? (process → ordered · comparison → facing · hierarchy → nested · causal → flow · parallel → index) — any mismatch is a structural lie; fix the *layout*, not the prose.
- [ ] **Spine-as-a-sentence test.** Read the section order / headings top to bottom as one sentence. Does it narrate the knowledge's actual structure? If the sections could be **shuffled with no loss**, the layout is carrying no structure — reorder it by the shape.

If it fails, the fix is structural (reorder / regroup / swap parallel-for-ordered), not cosmetic. Do it **before** you touch tokens. And if the check is genuinely ambiguous — two shapes compete and the choice materially changes the page — that is a §2C clarification (ask the user), not a silent guess; a clear dominant shape, though, is never a reason to ask, just build it.
