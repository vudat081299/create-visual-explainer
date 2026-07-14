# Plot Quality — Pre-flight Checklist

Read this whenever the page contains any plot. Visual styling tokens are in `components/charts.md`. This file is about **what makes a plot correct and informative**, not how to color it.

A plot can look beautiful (right colors, right typography) and still be useless or misleading. This file prevents that.

**Quick map**: §1 Should this be a plot at all? (L9–29) · §2 The required-annotations checklist (L30–59) · §3 Choosing axis ranges (L60–92) · §4 The plot lie detector (L93–111) · §5 Render-bug checks (after generating) (L112–141) · §6 Caption template (L142–163) · §7 Domain-specific gotchas (L164–202) · §8 The "would I trust this plot from a stranger?" test (L203–216) · §9 Chart-type + palette quick reference (L217–235)

---

## 1. Should this be a plot at all?

Before drawing anything, decide if a plot is the right format. Many "charts" should be a number, a sentence, or a table.

| Information | Best format | Why |
|---|---|---|
| One number ("accuracy = 94.2%") | Stat card | A 1-bar chart is silly |
| 2-5 numbers, no time/sequence | Table or labeled stats | A 5-bar chart is silly too |
| Trend over time, ≤5 series | Line chart | This is what line charts are for |
| Distribution of one variable | Histogram or violin | Don't use bar chart for continuous data |
| Joint distribution of two | Scatter | Heatmap if dense |
| Categorical comparison, ≤8 categories | Bar (horizontal if labels long) | |
| Categorical comparison, >8 categories | Reconsider — split, facet, or filter | A bar chart with 30 bars is unreadable |
| Part-to-whole, ≤4 slices | Stacked bar (preferred) or pie | Pie struggles with comparison |
| Part-to-whole, >4 slices | Stacked bar always | Pie is unreadable |
| Geographic data | Map | Not a heatmap unless data is naturally a grid |

**Rule of thumb**: if you can convey the finding in one sentence and a number, do that instead. The plot exists when seeing the *shape* matters.

---

## 2. The required-annotations checklist

Every plot must have these. Missing any one of them = the plot is incomplete.

### Always required

- [ ] **Title or chart caption** — what the plot shows in 5-10 words. Not "Loss curve" but "Training loss decreases steadily; validation loss plateaus around epoch 30".
- [ ] **X-axis label with units** — "Epoch" alone is borderline; "Epoch (out of 100)" is better.
- [ ] **Y-axis label with units** — "Accuracy (%)" not just "Accuracy". "Time (ms)" not just "Time".
- [ ] **Tick labels with consistent precision** — if you're showing 0.92, 0.93, 0.94, don't suddenly show 0.945. Pick decimals once.

### Required if applicable

- [ ] **Legend** — if there is more than one series. Place it in `.chart-actions` (top-right) or use the `.chart-legend` pill pattern below the plot.
- [ ] **Reference line** — if comparing to a baseline ("random performance = 50%"), state line explicitly with a labeled `.chart-hline`.
- [ ] **Source / dataset** — for any real-data plot, the source goes in `.chart-footer`: "MNIST · 60k train / 10k test · v1.0".
- [ ] **Sample size** — if showing aggregated statistics (means, medians), state n: `n = 1,247` in footer.
- [ ] **Confidence band** — if the underlying numbers have uncertainty, show ±1σ or 95% CI. Don't pretend point estimates are exact.

### When to add an annotation callout

If the plot has *one specific feature* the reader should look at first, drop a labeled callout:
- "← peak at epoch 47"
- "↓ regime change after sample 200"
- "← train/val divergence starts here (overfitting onset)"

Use the `.chart-vline` / `.chart-hline` patterns from `components/charts.md` §5. A plot with a salient feature and no annotation is a missed opportunity.

---

## 3. Choosing axis ranges

This is where most plots lie, intentionally or not.

### Y-axis range

- **Default to including zero** for any quantity where zero is meaningful (counts, dollars, accuracy, magnitudes).
- **Don't include zero** if it would compress the signal pointlessly (e.g., human body temperature plotted on 0-40°C — the variation lives in 36-39, so plot 36-39 with a clear "axis truncated" note in the caption).
- If you truncate, **say so explicitly** in the caption: *"Y-axis truncated to highlight variation; minimum value shown is 36.5°C, not 0."*
- **Never use a non-zero baseline for bar charts.** Bar lengths encode magnitude; truncating distorts them. Use a line/scatter chart instead.

### X-axis range

- **Time series**: pad each end by ~3% so first/last points aren't on the canvas edge.
- **Distributions**: extend to ±3σ around the mean unless the distribution has heavy tails — then go further.
- **Comparisons**: identical x-ranges across compared plots. Reader should not have to do scale arithmetic.

### Tick density

- Aim for **5-7 major ticks per axis**. More than 10 = clutter; fewer than 4 = vague.
- **Round numbers preferred**: 0, 10, 20, 30 — not 7, 14, 21, 28.
- **Powers of 10 for log axes**: $10^0, 10^1, 10^2, 10^3$ — minor ticks at 2, 5 are fine.
- **Time axes**: pick natural units (every Monday, every month start, every quarter), not raw equal divisions.

### Log scale: when to use

- **Yes log** when the data spans 2+ orders of magnitude AND the proportional change matters more than the absolute change. Examples: training loss over epochs, file sizes, populations, frequencies.
- **No log** when the data is bounded and roughly linear (accuracy 0-1, percentages with similar magnitudes).
- **Always label log axes clearly**: write "Loss (log scale)" not just "Loss".
- **Tick labels**: use scientific notation or the value (`1e-3` or `0.001` — pick one and stick to it).

---

## 4. The plot lie detector

Common dishonest patterns. Not always intentional, but always worth checking.

| Pattern | Why it lies | Fix |
|---|---|---|
| Truncated y-axis on bar chart | Exaggerates differences | Either start at 0, or use line chart |
| Dual y-axis with different scales | Implies correlation between unrelated series | Use two stacked plots instead |
| Cherry-picked time range | Hides longer-term context | Show full available range, then zoom if needed |
| 3D effects on 2D data | Distorts perceived magnitudes | Always 2D for quantitative comparison |
| Missing baseline | Reader doesn't know what's "normal" | Add reference line for prior, baseline, or random performance |
| Smoothing without disclosure | Invented signal that wasn't in raw data | Plot raw data + smoothed line, label both |
| Aggregating heterogeneous groups | Simpson's paradox territory | Plot subgroups separately |
| Aspect ratio squashing | Trends look more or less steep than they are | "Bank to 45°" rule: choose aspect so dominant slope ≈ 45° |
| Rebased y at 100 without disclosure | Hides absolute magnitudes | State the rebase date and value |
| Filling under non-cumulative line | Implies cumulative quantity | Only fill area under cumulative or density curves |

---

## 5. Render-bug checks (after generating)

Once the plot is drawn, scan for these bugs. They're shockingly common in generated code.

### Layout bugs
- [ ] Are any data points clipped at canvas edges? (Off-canvas points = silent data loss.)
- [ ] Do tick labels overlap? (Especially x-axis labels in time series.)
- [ ] Is the legend covering data?
- [ ] Are gridlines drawn over data instead of under?
- [ ] On retina displays, are lines/text crisp? (DPR scaling correctly?)

### Data bugs
- [ ] Do tick labels match the values? (E.g., axis says "Epoch" but values jump 0, 5, 10, 15 — is each really 5 epochs?)
- [ ] Are tick labels rounded so two adjacent ones look identical? ("1.000, 1.000, 1.001"? Increase precision or use scientific notation.)
- [ ] Is the y-axis really starting where the chart claims it does?
- [ ] Are dates parsed correctly? (Month-day vs day-month is a perennial bug.)
- [ ] Are missing values handled? (gaps in line vs interpolated vs zero — pick one explicitly)

### Color bugs
- [ ] Are color-blind-unsafe pairs used? (Red+green is the classic; check yellow+green and blue+purple too.)
- [ ] Is series 1 darker than series 2 in a sequential plot? (Categorical palette is fine; sequential needs ordering.)
- [ ] Does the same series have different colors across different plots on the same page? (Lock the color → series mapping at the top of the script.)

### Type-of-plot bugs
- [ ] Are categorical bars sorted meaningfully? (Alphabetical, by value, or by domain order — not by accident.)
- [ ] In a time series with one observation per day, is each day a tick or are they aggregated?
- [ ] In a histogram, is the bin width chosen — or did you accept the library default of 10 bins regardless of n?

---

## 6. Caption template

A good caption has three parts:

> **[Setup]** — what was measured, on what data, with what method.
> **[Finding]** — what the reader should notice. The single sentence that justifies the plot.
> **[Caveat]** — the most important limitation a careful reader would object to, addressed before they raise it.

### Example: bad caption

> *Figure 3. Training loss curve.*

This is just the title repeated. Says nothing.

### Example: good caption

> **Figure 3.** Cross-entropy loss on the CIFAR-10 train set over 100 epochs (n = 50,000), using SGD with momentum 0.9 and learning rate 0.01. **Loss drops sharply in the first 20 epochs, then plateaus around 0.15 — typical of overparameterized models reaching the interpolation regime.** Note: validation loss is shown separately in Figure 4; this plot is *not* a generalization curve.

Setup ✓, finding ✓, caveat ✓. Reader can use the plot productively.

---

## 7. Domain-specific gotchas

### Time series

- **Timezone**: state which one. UTC is safest for cross-comparison.
- **Missing values**: explicit gaps preferred over interpolation. If you interpolate, label.
- **Irregular sampling**: don't render as a line at uniform spacing — use a scatter or step line with actual timestamps.
- **Seasonality**: if data is seasonal (daily, weekly, yearly), consider showing one period overlaid with all others, in addition to the time series.

### Distributions

- **Histogram bin width**: too narrow → noisy; too wide → loses structure. Try Freedman-Diaconis: $\text{bin width} = 2 \cdot \text{IQR}/n^{1/3}$. Or use a violin/density plot to sidestep the choice.
- **KDE bandwidth**: state your choice. Default bandwidth selectors give different curves for the same data.
- **Log-scale x for heavy-tailed**: incomes, file sizes, citation counts. Otherwise the head dominates.

### Comparison plots (A/B, before/after)

- **Same axis scale on both panels.** Always.
- **Same preprocessing.** If you smoothed A, smooth B identically.
- **Same color encoding for the same role.** "Treatment" should be the same color across all panels.
- **Annotate the difference.** A side-by-side without a "this is the takeaway" is just data, not analysis.

### ML training curves

- **Show both train and validation** unless you're explicitly studying training behavior in isolation.
- **Label epochs vs. steps** clearly. They're not the same.
- **Loss vs. accuracy on different y-scales**: don't put them on the same chart with dual axes; show side-by-side.
- **For loss, log-y is usually better.** For accuracy, linear is fine.
- **Mark significant events**: when LR was reduced, when model was loaded from checkpoint, when augmentation was added. Use `.chart-vline`.

### Heatmaps / matrices

- **Diverging palette for ±**: residuals, correlations, deltas. Zero must read as background.
- **Sequential palette for [0, max]**: densities, counts. Light = low or dark = low — pick once and use consistently across the page.
- **Annotate values inside cells** if the matrix is small (≤ 10×10). For larger matrices, just the colorbar.
- **Square cells**: a 5×5 matrix should render with square cells unless the underlying axes are different things.

---

## 8. The "would I trust this plot from a stranger?" test

Final check. Imagine you found this plot on someone else's website with no surrounding text. Could you:

1. Tell what it's showing? (title or caption answers)
2. Tell what units? (axis labels)
3. Tell when/where the data is from? (footer or caption)
4. Tell what conclusion the author wants you to draw? (annotation or caption)
5. Detect if you're being misled? (no truncated bar chart, no dual y-axis, no missing source)

If any answer is "no", the plot isn't ready.

---

## 9. Chart-type + palette quick reference

Quick decision lookup: given the data shape you have, this picks the chart type AND the palette/opacity to use. (Used to live in `components/charts.md` §8 — moved here because choosing the chart type is a pre-flight decision, not a styling concern. The render-side details — how to lay out axes, render the legend, animate — are still in `components/charts.md`.)

| Goal | Chart type | Notes |
|---|---|---|
| Trend over time | Line | Use `--cat-N` per series. ≤4 series; more → small multiples |
| Compare categories | Bar (vertical) | If labels are long, switch to horizontal bar |
| Distribution of one variable | Histogram | Use single `--accent-blue` fill, `0.20` opacity |
| Joint distribution of two | Scatter | Use `--accent-cyan` for points; opacity `0.5` if dense |
| Correlation matrix | Heatmap | Use **diverging** palette (`--div-*`); zero must = `--border` |
| Density / 2D distribution | Heatmap or contour | **Sequential** palette (`--seq-*`); never categorical |
| Part-to-whole | Stacked bar (preferred over pie) | Pie charts only for ≤4 slices |
| Confidence interval | Line + band | Mean line at full opacity, band at 0.10 fill |
| Step function / discrete events | Step line | `tension: 0` in Chart.js, `type: 'stepAfter'` in Recharts |

**Rule of thumb**: if the user asks for a pie chart with >4 slices, suggest a horizontal bar instead — it's almost always more readable.

