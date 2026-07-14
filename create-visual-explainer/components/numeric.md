# Numeric Display Patterns

Primitives for showing numbers compactly: progress bars, meters, sparklines, count-ups, and stat-card grids. Read this when the page has KPI values, learning progress indicators, or any small numeric chrome that doesn't need a full-size chart from `components/charts.md`.

For *dashboard chrome* that's not numeric (segmented control, filter chip, banner, avatar), see `components/dashboard-chrome.md`. For full-size charts and chart styling, see `components/charts.md`. The basic `.stat` card lives in `components/core.md` §12 — this file's §5 covers laying out multiple stat cards in a row.

**Quick map**: §1 Progress bar (linear) (L11–71) · §2 Meter (semantic, with thresholds) (L72–118) · §3 Sparkline (inline mini-chart) (L119–206) · §4 Count-up animation (L207–257) · §5 Stat-card grid (L258–292) · §6 Numeric-display selection guide (L293–303)

---

## 1. Progress bar (linear)

Generic progress indicator. Different from `.reading-progress` (top-of-page, in `usecases/longform-study.md`) — this one is in-flow and labelled.

```html
<div class="progress" role="progressbar" aria-valuenow="68" aria-valuemin="0" aria-valuemax="100">
  <div class="progress-track">
    <div class="progress-fill" style="width: 68%;"></div>
  </div>
  <div class="progress-meta">
    <span class="progress-label">EPOCHS COMPLETED</span>
    <span class="progress-value">68 / 100</span>
  </div>
</div>
```

```css
.progress {
  display: flex; flex-direction: column;
  gap: var(--sp-2);
  margin: var(--sp-3) 0;
}
.progress-track {
  height: 6px;
  background: var(--border);
  border-radius: var(--r-pill);
  overflow: hidden;
}
.progress-fill {
  height: 100%;
  background: var(--accent-green);
  border-radius: inherit;
  transition: width var(--dur-base) var(--ease-out);
}
.progress-meta {
  display: flex; justify-content: space-between;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
}
.progress-label { color: var(--text-muted); text-transform: uppercase; }
.progress-value { color: var(--text-primary); font-variant-numeric: tabular-nums; }

/* Variants by status */
.progress[data-status="warning"] .progress-fill { background: var(--accent-yellow); }
.progress[data-status="danger"]  .progress-fill { background: var(--accent-red); }
.progress[data-status="info"]    .progress-fill { background: var(--accent-blue); }

/* Indeterminate — for unknown duration tasks */
.progress[data-state="indeterminate"] .progress-fill {
  width: 30% !important;
  animation: indeterminate 1.4s var(--ease-in-out) infinite;
}
@keyframes indeterminate {
  0%   { transform: translateX(-100%); }
  100% { transform: translateX(400%); }
}
```

---

## 2. Meter (semantic progress with thresholds)

Use when the value has named ranges (e.g. CPU usage with `low / medium / high` thresholds). The native `<meter>` element handles this with proper a11y.

```html
<div class="meter">
  <div class="meter-meta">
    <span class="meter-label">GPU UTILIZATION</span>
    <span class="meter-value">82%</span>
  </div>
  <meter class="meter-bar" min="0" low="40" high="80" optimum="60" max="100" value="82"></meter>
</div>
```

```css
.meter { display: flex; flex-direction: column; gap: var(--sp-2); }
.meter-meta {
  display: flex; justify-content: space-between;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
}
.meter-label { color: var(--text-muted); text-transform: uppercase; }
.meter-value { color: var(--text-primary); font-variant-numeric: tabular-nums; }

.meter-bar {
  width: 100%;
  height: 6px;
  -webkit-appearance: none; appearance: none;
  border: none;
  background: var(--border);
  border-radius: var(--r-pill);
  overflow: hidden;
}
/* Webkit */
.meter-bar::-webkit-meter-bar { background: var(--border); border: none; }
.meter-bar::-webkit-meter-optimum-value      { background: var(--accent-green);  }
.meter-bar::-webkit-meter-suboptimum-value   { background: var(--accent-yellow); }
.meter-bar::-webkit-meter-even-less-good-value { background: var(--accent-red); }
/* Firefox */
.meter-bar:-moz-meter-optimum::-moz-meter-bar      { background: var(--accent-green); }
.meter-bar:-moz-meter-sub-optimum::-moz-meter-bar  { background: var(--accent-yellow); }
.meter-bar:-moz-meter-sub-sub-optimum::-moz-meter-bar { background: var(--accent-red); }
```

---

## 3. Sparkline (inline mini-chart)

Tiny inline chart for displaying a trend in 1cm of horizontal space — usually next to a stat card or in a table row. Best implemented as inline SVG.

```html
<div class="sparkstat">
  <div class="sparkstat-header">
    <span class="sparkstat-label">DAILY ACTIVE USERS</span>
    <span class="sparkstat-delta sparkstat-delta-up">+12.3%</span>
  </div>
  <div class="sparkstat-body">
    <span class="sparkstat-value">1,247</span>
    <svg class="sparkline" viewBox="0 0 100 30" preserveAspectRatio="none" aria-hidden="true">
      <polyline points="0,22 10,18 20,20 30,15 40,17 50,12 60,14 70,8 80,10 90,5 100,4"
                fill="none" stroke="currentColor" stroke-width="1.5"
                stroke-linecap="round" stroke-linejoin="round"/>
      <circle cx="100" cy="4" r="2" fill="currentColor"/>
    </svg>
  </div>
</div>
```

```css
.sparkstat {
  display: flex; flex-direction: column;
  gap: var(--sp-2);
  padding: var(--sp-4) var(--sp-5);
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-md);
}
.sparkstat-header {
  display: flex; justify-content: space-between;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
}
.sparkstat-label { color: var(--text-muted); text-transform: uppercase; }
.sparkstat-delta { font-variant-numeric: tabular-nums; }
.sparkstat-delta-up   { color: var(--accent-green); }
.sparkstat-delta-down { color: var(--accent-red); }

.sparkstat-body {
  display: grid;
  grid-template-columns: auto 1fr;
  align-items: end;
  gap: var(--sp-4);
}
.sparkstat-value {
  font-family: var(--font-display);
  font-size: 36px;
  line-height: 1;
  color: var(--text-primary);
  font-variant-numeric: tabular-nums;
}
.sparkline {
  height: 36px;
  width: 100%;
  color: var(--accent-green);
}
.sparkstat-delta-down ~ * .sparkline,
.sparkstat[data-trend="down"] .sparkline { color: var(--accent-red); }
```

### Generating sparkline points from data

```js
function sparklinePoints(values, width = 100, height = 30, padding = 2) {
  const min = Math.min(...values);
  const max = Math.max(...values);
  const range = max - min || 1;
  const stepX = (width - padding * 2) / (values.length - 1);
  return values.map((v, i) => {
    const x = padding + i * stepX;
    const y = padding + (height - padding * 2) * (1 - (v - min) / range);
    return `${x.toFixed(1)},${y.toFixed(1)}`;
  }).join(' ');
}

document.querySelectorAll('.sparkline[data-values]').forEach(svg => {
  const values = svg.dataset.values.split(',').map(Number);
  const polyline = svg.querySelector('polyline');
  if (polyline) polyline.setAttribute('points', sparklinePoints(values));
});
```

---

## 4. Count-up animation (for live values)

Numeric value that animates from 0 → target on first paint. Subtle "the data just arrived" feedback. Use sparingly — not on every number.

```html
<span class="count-up" data-target="94.2" data-decimals="1">0</span>%
```

```js
function countUp(el, duration = 800) {
  const target = parseFloat(el.dataset.target);
  const decimals = parseInt(el.dataset.decimals || '0', 10);
  const start = performance.now();

  function tick(now) {
    const t = Math.min((now - start) / duration, 1);
    const eased = 1 - Math.pow(1 - t, 3); // ease-out cubic
    const value = target * eased;
    el.textContent = value.toFixed(decimals);
    if (t < 1) requestAnimationFrame(tick);
  }
  requestAnimationFrame(tick);
}

// Run when in viewport
const obs = new IntersectionObserver((entries, observer) => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      countUp(e.target);
      observer.unobserve(e.target);
    }
  });
});
document.querySelectorAll('.count-up').forEach(el => obs.observe(el));

// Reduced motion: skip the animation
if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
  document.querySelectorAll('.count-up').forEach(el => {
    el.textContent = parseFloat(el.dataset.target).toFixed(parseInt(el.dataset.decimals || '0'));
  });
}
```

```css
.count-up { font-variant-numeric: tabular-nums; }
```

Note: count-up is *optional* per `SKILL.md` §2C — only include if user explicitly asks.

---

## 5. Stat-card grid (the typical dashboard row)

Composition pattern — not a new component. Lays out 3-5 `.stat` cards (from `components/core.md` §12) in a responsive grid.

```html
<div class="stat-grid">
  <div class="card">
    <div class="stat">
      <span class="stat-label">VAL ACCURACY</span>
      <span class="stat-value">94.2<span class="stat-unit">%</span></span>
      <span class="stat-delta stat-delta-up">↑ 2.1pp</span>
    </div>
  </div>
  <div class="card">
    <div class="stat">
      <span class="stat-label">TRAIN LOSS</span>
      <span class="stat-value">0.18</span>
      <span class="stat-delta stat-delta-down">↓ 0.04</span>
    </div>
  </div>
  <!-- ... -->
</div>
```

```css
.stat-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: var(--sp-4);
  margin: var(--sp-6) 0;
}
```

---

## 6. When to use which numeric display

| Need to show | Use | Notes |
|---|---|---|
| One key metric, prominent | `.stat` (in card) — `components/core.md` §12 | Bebas Neue 40-64px |
| Many metrics in a row | `.stat-grid` (this file §5) | 3-5 stat cards |
| Trend hint next to value | `.sparkstat` (this file §3) | Stat + inline sparkline |
| Progress towards a target | `.progress` (this file §1) | Linear bar with label |
| Health/utilization (with bands) | `.meter` (this file §2) | Native `<meter>` element |
| Live-feeling number | `.count-up` (this file §4) | Animates 0 → target on view |
