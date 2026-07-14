# Charts & Data Visualization

Patterns for charts in DS dashboards, ML demos, and statistical pages. Read this whenever the task involves any chart — line, bar, scatter, heatmap, distribution plot, etc.

The categorical / sequential / diverging palettes are in `foundations/tokens.md` §1.5 — they're already theme-aware. This file covers everything around the data: container, axes, legend, tooltip, annotations. **For chart-type *selection* (which chart for which data, with palette/opacity defaults), see `foundations/plot-quality.md` §9 — that's a pre-flight decision, not a render concern.**

**Quick map**: §1 Chart container (L11–68) · §2 Axes (L69–109) · §3 Legend (use pills) (L110–148) · §4 Tooltip (L149–204) · §5 Annotations (L205–246) · §6 No-data and error states (L247–267) · §7 Library-specific integration (L268–351) · §8 Animation guidance for charts (L352–358)

---

## 1. Chart container

```css
.chart-container {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: var(--sp-5) var(--sp-6);
  isolation: isolate;            /* prevents body grid texture from showing through */
  position: relative;
}

.chart-header {
  display: flex; align-items: flex-start; justify-content: space-between;
  margin-bottom: var(--sp-4);
  gap: var(--sp-4);
}

.chart-title {
  font-family: var(--font-display);
  font-size: 22px;
  letter-spacing: 0.01em;
  color: var(--text-primary);
}

.chart-subtitle {
  font-family: var(--font-body);
  font-size: var(--fs-xs);
  color: var(--text-muted);
  margin-top: var(--sp-1);
}

/* Right-aligned actions area: legend pills, time-range toggles, export button */
.chart-actions { display: flex; gap: var(--sp-2); flex-wrap: wrap; }

.chart-body {
  width: 100%;
  /* aspect ratio for responsive height — adjust per chart type */
  aspect-ratio: 16 / 9;
  position: relative;
}

.chart-footer {
  margin-top: var(--sp-4);
  padding-top: var(--sp-3);
  border-top: 1px solid var(--border);
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.1em;
  color: var(--text-dim);
  text-transform: uppercase;
}
```

**Why `isolation: isolate`**: the body grid texture (from `foundations/tokens.md` §1.6) competes with chart gridlines. Isolating the chart container creates a new stacking context that hides the body grid behind the card background.

---

## 2. Axes

These are CSS rules for SVG-based charts (D3, custom). For canvas charts (Chart.js, Plotly), pass equivalent values via the library's options.

```css
.axis-line   { stroke: var(--border); stroke-width: 1; }
.axis-tick   { stroke: var(--border); stroke-width: 1; }

.axis-label,
.tick-label {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.1em;
  fill: var(--text-muted);
  text-transform: uppercase;
}

/* Axis title — descriptive label for the axis */
.axis-title {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  fill: var(--text-secondary);
  text-transform: uppercase;
}

/* Gridlines */
.gridline {
  stroke: var(--hairline);
  stroke-width: 1;
  shape-rendering: crispEdges;  /* avoids antialias blur on horizontal lines */
}

/* Zero baseline (emphasized) */
.gridline-zero { stroke: var(--border); stroke-dasharray: none; }
```

**Tabular numerals on tick labels**: when generating ticks via SVG `<text>`, add `font-variant-numeric: tabular-nums` so axis numbers align across rows.

---

## 3. Legend (use pills)

The legend reuses `.pill` component. Each pill's left dot uses the categorical palette directly.

```html
<div class="chart-legend">
  <span class="pill"><i style="background:var(--cat-1)"></i> Train accuracy</span>
  <span class="pill"><i style="background:var(--cat-2)"></i> Validation accuracy</span>
  <span class="pill"><i style="background:var(--cat-4)"></i> Test loss</span>
</div>
```

```css
.chart-legend {
  display: flex; flex-wrap: wrap;
  gap: var(--sp-2);
  margin-top: var(--sp-3);
}
.chart-legend .pill {
  background: transparent;
  color: var(--text-secondary);
  border-color: var(--border);
}
.chart-legend .pill i {
  display: inline-block;
  width: 8px; height: 8px;
  border-radius: 50%;
  margin-right: var(--sp-2);
}

/* Interactive legend — click to toggle series */
.chart-legend .pill[data-active="false"] {
  opacity: 0.4;
}
.chart-legend .pill { cursor: pointer; transition: opacity var(--dur-fast); }
```

---

## 4. Tooltip

```css
.chart-tooltip {
  position: absolute;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-sm);
  padding: var(--sp-3) var(--sp-4);
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
  color: var(--text-primary);
  pointer-events: none;
  z-index: var(--z-dropdown);
  /* Allowed shadow: floats above chart */
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
  min-width: 160px;
}
[data-theme="light"] .chart-tooltip {
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
}

.chart-tooltip-header {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
  padding-bottom: var(--sp-2);
  margin-bottom: var(--sp-2);
  border-bottom: 1px solid var(--border);
}

.chart-tooltip-row {
  display: flex; justify-content: space-between;
  align-items: center;
  gap: var(--sp-4);
  padding: 2px 0;
}
.chart-tooltip-row i {
  display: inline-block;
  width: 6px; height: 6px;
  border-radius: 50%;
  margin-right: var(--sp-2);
}
.chart-tooltip-row .label { color: var(--text-secondary); }
.chart-tooltip-row .value {
  color: var(--text-primary);
  font-variant-numeric: tabular-nums;
}
```

**Layout**: header (x-axis value, e.g. timestamp) → rows (one per series, color dot + label + value).

---

## 5. Annotations

Reference lines and bands on charts (e.g. mean line, threshold, training-end marker).

```css
/* Vertical reference line (e.g. "epoch 50") */
.chart-vline {
  stroke: var(--accent-yellow);
  stroke-width: 1;
  stroke-dasharray: 4 4;
}
.chart-vline-label {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.1em;
  fill: var(--accent-yellow);
  text-transform: uppercase;
}

/* Horizontal threshold (e.g. "target accuracy 0.95") */
.chart-hline {
  stroke: var(--accent-green);
  stroke-width: 1;
  stroke-dasharray: 4 4;
}

/* Confidence band (e.g. ±1σ around mean) */
.chart-band {
  fill: rgb(var(--accent-blue-rgb) / 0.10);
  stroke: rgb(var(--accent-blue-rgb) / 0.20);
  stroke-width: 1;
}

/* Highlighted region (e.g. anomaly window) */
.chart-region {
  fill: rgb(var(--accent-red-rgb) / 0.08);
  stroke: rgb(var(--accent-red-rgb) / 0.20);
}
```

---

## 6. No-data and error states

```html
<div class="chart-container">
  <div class="chart-header">
    <h3 class="chart-title">Loss Over Epochs</h3>
  </div>
  <div class="chart-body">
    <div class="empty">
      <span class="empty-icon">∅</span>
      <p class="empty-title">No data yet</p>
      <p class="empty-msg">Start a training run to see loss curves here.</p>
    </div>
  </div>
</div>
```

For loading: replace `.chart-body` content with `<div class="skeleton" style="width:100%;height:100%"></div>`.

---

## 7. Library-specific integration

### Chart.js

```js
import Chart from 'chart.js/auto';

const css = getComputedStyle(document.documentElement);
const cat = (n) => css.getPropertyValue(`--cat-${n}`).trim();

new Chart(canvas, {
  type: 'line',
  data: {
    labels: epochs,
    datasets: [
      { label: 'Train',      data: train, borderColor: cat(1), backgroundColor: cat(1) + '20', tension: 0.3 },
      { label: 'Validation', data: val,   borderColor: cat(2), backgroundColor: cat(2) + '20', tension: 0.3 },
    ]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: {
      legend: { display: false },  // we use our own .chart-legend
      tooltip: { enabled: false }, // we use our own .chart-tooltip
    },
    scales: {
      x: {
        grid:   { color: css.getPropertyValue('--hairline') },
        ticks:  { color: css.getPropertyValue('--text-muted'), font: { family: 'JetBrains Mono', size: 10 } },
        border: { color: css.getPropertyValue('--border') },
      },
      y: {
        grid:   { color: css.getPropertyValue('--hairline') },
        ticks:  { color: css.getPropertyValue('--text-muted'), font: { family: 'JetBrains Mono', size: 10 } },
        border: { color: css.getPropertyValue('--border') },
      },
    },
  }
});
```

### Recharts (React)

```jsx
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip } from 'recharts';

const tokens = {
  border:  getComputedStyle(document.documentElement).getPropertyValue('--border').trim(),
  hairline:getComputedStyle(document.documentElement).getPropertyValue('--hairline').trim(),
  muted:   getComputedStyle(document.documentElement).getPropertyValue('--text-muted').trim(),
  cat1:    getComputedStyle(document.documentElement).getPropertyValue('--cat-1').trim(),
  cat2:    getComputedStyle(document.documentElement).getPropertyValue('--cat-2').trim(),
};

<LineChart data={data}>
  <CartesianGrid strokeDasharray="0" stroke={tokens.hairline} />
  <XAxis stroke={tokens.border} tick={{ fill: tokens.muted, fontFamily: 'JetBrains Mono', fontSize: 10 }} />
  <YAxis stroke={tokens.border} tick={{ fill: tokens.muted, fontFamily: 'JetBrains Mono', fontSize: 10 }} />
  <Tooltip contentStyle={{ background: 'var(--card)', border: '1px solid var(--border)', borderRadius: 6 }} />
  <Line type="monotone" dataKey="train" stroke={tokens.cat1} strokeWidth={2} dot={false} />
  <Line type="monotone" dataKey="val"   stroke={tokens.cat2} strokeWidth={2} dot={false} />
</LineChart>
```

### Plotly

```js
const layout = {
  paper_bgcolor: 'transparent',
  plot_bgcolor:  'transparent',
  font: { family: 'JetBrains Mono', size: 11, color: getCSS('--text-muted') },
  xaxis: { gridcolor: getCSS('--hairline'), linecolor: getCSS('--border'), zerolinecolor: getCSS('--border') },
  yaxis: { gridcolor: getCSS('--hairline'), linecolor: getCSS('--border'), zerolinecolor: getCSS('--border') },
  colorway: [getCSS('--cat-1'), getCSS('--cat-2'), getCSS('--cat-3'), getCSS('--cat-4'), getCSS('--cat-5')],
  margin: { l: 60, r: 20, t: 20, b: 50 },
};
function getCSS(name) {
  return getComputedStyle(document.documentElement).getPropertyValue(name).trim();
}
```

---

## 8. Animation guidance for charts

- **Initial draw**: stagger series entry by 80–120ms each. Animate `stroke-dasharray` from full length to 0 for line charts, or `transform: scaleY(0)` → `scaleY(1)` for bars (origin-y at baseline).
- **Hover**: highlight one series at full opacity, fade others to `0.3`. Transition `opacity` only.
- **Data updates**: animate `d` attribute (path) over 400ms with `--ease-out`. For bar values, animate height on `transform`, not on the `height` attribute.
- **Reduced motion**: disable all chart entrance animations under `prefers-reduced-motion`.
