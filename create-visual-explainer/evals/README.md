# Evals — How to use this folder

This folder contains test prompts + assertions for verifying the skill works as intended. It does NOT replace human review — it is a **regression safety net**.

## Files

- `evals.json` — 6 test prompts (4 positive, 2 negative-trigger) with success and failure criteria.
- `results/` — created when you run evals. Organized by `iteration-N/eval-K/` so you can compare across edits.

## What each eval tests

| ID | Type | Tests |
|---|---|---|
| 1 | Positive | Interactive explainer loadout: sliders, KaTeX, plot annotations, triple-opacity |
| 2 | Positive | Dashboard loadout: sidebar nav, stat cards, theme toggle, isolation:isolate |
| 3 | Positive | Longform study note loadout: TOC, collapsible quiz, polished-SaaS dial |
| 4 | Positive (rule 13) | Prose-first when user opts out of visuals — skill must NOT force a chart |
| 5 | Negative trigger | Code debugging task — skill must NOT load |
| 6 | Negative trigger | Plain email task — skill must NOT load |

## How to run (manual, in Claude.ai)

For each eval prompt:

1. Open a **fresh** conversation in Claude.ai (no prior context).
2. Make sure the skill `create-visual-explainer` is in `/mnt/skills/user/`.
3. Paste the `prompt` field from `evals.json`.
4. Save the output to `evals/results/iteration-1/eval-<id>/output.html` (or `.txt`/`.md` for non-HTML outputs).
5. Score against `success_criteria` and `failure_criteria` from the JSON.
6. Repeat 3 times per prompt (LLM stochastic — report median pass-rate).

For negative-trigger evals (5, 6): the *only* thing being checked is whether the skill loaded. If it did, the description in `SKILL.md` frontmatter is too broad and needs trimming.

## Scoring

- **Success**: ≥80% of `success_criteria` pass per eval, AND zero `failure_criteria` triggered.
- **Negative evals**: skill loaded ZERO out of 3 runs. One trigger out of 3 = description too broad.

## When to update evals

- **Add an eval** when you discover a real-world prompt the skill handled badly. That prompt becomes the regression test.
- **Tighten an assertion** when an output is technically passing but reading it you think "this isn't quite right" — find the concrete rule it broke and add it.
- **Remove an eval** only if the loadout it tested no longer exists in the skill (e.g. a feature was removed).

## Note on `auto: true` criteria

Criteria marked `auto: true` are scriptable — they can be checked with grep, regex, DOM parse, or word count. Run them manually with shell tools (`grep`, `htmlq`, `python -c`, etc.) against the saved output files.

Criteria marked `auto: false` need human judgment (e.g. "Layer 1 → Layer 2 → Layer 3 order respected" — a regex can't reliably detect pedagogical layering).
