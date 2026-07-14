# Components

CSS for core UI components. All components use the tokens from `foundations/tokens.md` and follow the triple-opacity rule. Components auto-adapt to dark/light theme via the CSS variables.

Read this whenever building a page that has interactive UI elements.

**Quick map**: §1 Buttons (L11–66) · §2 Form inputs (L67–168) · §3 Cards (L169–195) · §4 Tables (L196–246) · §5 Badges/pills/code-tags (L247–337) · §6 Tooltip & Popover (L338–375) · §7 Modal/Dialog (L376–427) · §8 Toast/Alert (L428–479) · §9 Skeleton/Empty/Spinner (L480–533) · §10 Code blocks (DS/dev tutorials) (L534–601) · §11 Tabs (L602–631) · §12 Stat card (KPI) (L632–674)

---

## 1. Buttons

```css
.btn {
  display: inline-flex; align-items: center; gap: var(--sp-2);
  font-family: var(--font-body); font-weight: 500; font-size: var(--fs-sm);
  letter-spacing: 0.02em;
  padding: var(--sp-3) var(--sp-5);
  min-height: 40px;
  border-radius: var(--r-sm);
  border: 1px solid transparent;
  cursor: pointer;
  transition: background var(--dur-fast) var(--ease-out),
              border-color var(--dur-fast) var(--ease-out),
              transform var(--dur-instant) var(--ease-out);
}
.btn:active { transform: translateY(1px); }
.btn:focus-visible {
  outline: none;
  box-shadow: 0 0 0 3px rgb(var(--accent-green-rgb) / 0.30);
}
.btn:disabled { opacity: 0.4; cursor: not-allowed; }

/* Variants */
.btn-primary   { background: var(--accent-green); color: var(--bg); }
.btn-primary:hover:not(:disabled) { filter: brightness(1.1); }

.btn-secondary {
  background: var(--card); color: var(--text-primary);
  border-color: var(--border);
}
.btn-secondary:hover:not(:disabled) {
  border-color: rgb(var(--accent-green-rgb) / 0.5);
  background: var(--surface);
}

.btn-ghost     { background: transparent; color: var(--text-primary); }
.btn-ghost:hover:not(:disabled) { background: var(--hairline); }

.btn-danger {
  background: rgb(var(--accent-red-rgb) / 0.10);
  color: var(--accent-red);
  border-color: rgb(var(--accent-red-rgb) / 0.25);
}
.btn-danger:hover:not(:disabled) { background: rgb(var(--accent-red-rgb) / 0.18); }

/* Sizes */
.btn-sm { min-height: 32px; padding: var(--sp-2) var(--sp-4); font-size: var(--fs-xs); }
.btn-lg { min-height: 48px; padding: var(--sp-4) var(--sp-6); font-size: var(--fs-base); }

/* Mobile: enforce 44px touch target (Apple HIG) */
@media (max-width: 768px) { .btn { min-height: 44px; } }
```

---

## 2. Form inputs

```css
.input, .select, .textarea {
  width: 100%;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-sm);
  color: var(--text-primary);
  font-family: var(--font-body);
  font-size: var(--fs-base);
  padding: var(--sp-3) var(--sp-4);
  min-height: 40px;
  transition: border-color var(--dur-fast) var(--ease-out),
              box-shadow var(--dur-fast) var(--ease-out);
}
.input::placeholder { color: var(--text-dim); }
.input:hover { border-color: rgb(var(--accent-green-rgb) / 0.4); }
.input:focus {
  outline: none;
  border-color: var(--accent-green);
  box-shadow: 0 0 0 3px rgb(var(--accent-green-rgb) / 0.15);
}

/* Validation states */
.input[aria-invalid="true"] {
  border-color: var(--accent-red);
  box-shadow: 0 0 0 3px rgb(var(--accent-red-rgb) / 0.15);
}
.input[data-state="valid"] {
  border-color: var(--accent-green);
}

/* Slider — for hyperparameter / interactive demos */
.slider {
  -webkit-appearance: none; appearance: none;
  width: 100%; height: 4px;
  background: var(--border);
  border-radius: var(--r-pill);
  outline: none;
}
.slider::-webkit-slider-thumb {
  -webkit-appearance: none; appearance: none;
  width: 16px; height: 16px;
  background: var(--accent-green);
  border-radius: 50%;
  cursor: grab;
  border: 2px solid var(--bg);
}
.slider::-webkit-slider-thumb:active { cursor: grabbing; }
.slider::-moz-range-thumb {
  width: 16px; height: 16px;
  background: var(--accent-green);
  border-radius: 50%;
  cursor: grab;
  border: 2px solid var(--bg);
}

/* Switch — for binary toggles */
.switch {
  position: relative; display: inline-block;
  width: 36px; height: 20px;
}
.switch input { opacity: 0; width: 0; height: 0; }
.switch-track {
  position: absolute; inset: 0;
  background: var(--border); border-radius: var(--r-pill);
  transition: background var(--dur-fast);
  cursor: pointer;
}
.switch-track::before {
  content: ''; position: absolute;
  width: 14px; height: 14px; left: 3px; top: 3px;
  background: var(--text-primary); border-radius: 50%;
  transition: transform var(--dur-fast) var(--ease-out);
}
.switch input:checked + .switch-track { background: var(--accent-green); }
.switch input:checked + .switch-track::before {
  transform: translateX(16px);
  background: var(--bg);
}

/* Field group */
.field { display: flex; flex-direction: column; gap: var(--sp-2); }
.field label {
  font-family: var(--font-mono); font-size: var(--fs-label);
  letter-spacing: 0.15em; text-transform: uppercase;
  color: var(--text-muted);
}
.field .help  { font-size: var(--fs-xs); color: var(--text-muted); }
.field .error {
  font-size: var(--fs-xs);
  color: var(--accent-red);
  display: flex; align-items: center; gap: var(--sp-1);
}
.field .error::before { content: '!'; font-weight: 700; }
```

**Validation message pattern**: place `<span class="error">message</span>` right below the input. Use `aria-invalid="true"` and `aria-describedby="error-id"` on the input for screen readers.

---

## 3. Cards

```css
.card {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: var(--sp-5) var(--sp-6);
}
.card-hover { transition: border-color var(--dur-fast) var(--ease-out); }
.card-hover:hover { border-color: rgb(var(--accent-green-rgb) / 0.4); }

.card-header {
  display: flex; align-items: center; justify-content: space-between;
  padding-bottom: var(--sp-4);
  border-bottom: 1px solid var(--border);
  margin-bottom: var(--sp-4);
}
.card-title {
  font-family: var(--font-display);
  font-size: 22px; letter-spacing: 0.01em;
  color: var(--text-primary);
}
```

---

## 4. Tables

```css
.table-wrapper {
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  overflow: hidden;
}
.table-scroll { overflow-x: auto; }

table {
  width: 100%; border-collapse: collapse;
  font-family: var(--font-body);
}
thead th {
  background: var(--surface);
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
  text-align: left;
  padding: var(--sp-3) var(--sp-5);
  font-weight: 500;
  white-space: nowrap;
}
tbody td {
  padding: var(--sp-4) var(--sp-5);
  font-size: var(--fs-sm);
  color: var(--text-primary);
  border-bottom: 1px solid var(--border);
}
tbody tr:last-child td { border-bottom: none; }
tbody tr:hover { background: var(--hairline); }

td.num, th.num {
  text-align: right;
  font-variant-numeric: tabular-nums lining-nums;
}

/* Sticky first column for wide comparison tables */
.table-sticky-col th:first-child,
.table-sticky-col td:first-child {
  position: sticky; left: 0;
  background: var(--card);
  z-index: 1;
}
```

---

## 5. Badges, pills, code-tags

Three sizes/styles for status/category markers:

```css
/* INLINE BADGE — small status marker, monospace */
.badge {
  display: inline-flex; align-items: center;
  font-family: var(--font-mono);
  font-size: var(--fs-nano);
  padding: 1px 5px;
  border-radius: var(--r-xs);
  border: 1px solid;
  letter-spacing: 0.05em;
}
.badge-warning {
  background: rgb(var(--accent-yellow-rgb) / 0.10);
  color: var(--accent-yellow);
  border-color: rgb(var(--accent-yellow-rgb) / 0.25);
}
.badge-info {
  background: rgb(var(--accent-blue-rgb) / 0.10);
  color: var(--accent-blue);
  border-color: rgb(var(--accent-blue-rgb) / 0.25);
}
.badge-success {
  background: rgb(var(--accent-green-rgb) / 0.10);
  color: var(--accent-green);
  border-color: rgb(var(--accent-green-rgb) / 0.25);
}
.badge-danger {
  background: rgb(var(--accent-red-rgb) / 0.10);
  color: var(--accent-red);
  border-color: rgb(var(--accent-red-rgb) / 0.25);
}

/* PILL — rounded category tag, body font */
.pill {
  display: inline-flex; align-items: center;
  font-family: var(--font-body); font-weight: 500;
  font-size: var(--fs-xs);
  padding: 3px var(--sp-3);
  border-radius: var(--r-pill);
  border: 1px solid;
}
.pill-success {
  background: rgb(var(--accent-green-rgb) / 0.08);
  color: var(--accent-green);
  border-color: rgb(var(--accent-green-rgb) / 0.20);
}
.pill-warning {
  background: rgb(var(--accent-yellow-rgb) / 0.08);
  color: var(--accent-yellow);
  border-color: rgb(var(--accent-yellow-rgb) / 0.20);
}
.pill-danger {
  background: rgb(var(--accent-red-rgb) / 0.08);
  color: var(--accent-red);
  border-color: rgb(var(--accent-red-rgb) / 0.20);
}
.pill-info {
  background: rgb(var(--accent-blue-rgb) / 0.08);
  color: var(--accent-blue);
  border-color: rgb(var(--accent-blue-rgb) / 0.20);
}
.pill-neutral {
  background: var(--hairline);
  color: var(--text-secondary);
  border-color: var(--border);
}

/* CODE TAG — for syntax tokens, function signatures, tech tags */
.code-tag {
  display: inline-flex;
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
  padding: 3px var(--sp-2);
  background: rgb(var(--accent-blue-rgb) / 0.08);
  color: var(--accent-blue);
  border: 1px solid rgb(var(--accent-blue-rgb) / 0.20);
  border-radius: var(--r-xs);
}
```

**When to use which**:
- **Badge** (smallest): inline status next to a name/title — "Pro", "Beta", "v3"
- **Pill** (medium): category filters, content tags, status that's the primary signal
- **Code-tag**: technical labels — `useState`, `O(n log n)`, `numpy.array`

---

## 6. Tooltip & Popover

```css
.tooltip {
  position: absolute;
  background: var(--card);
  border: 1px solid var(--border);
  color: var(--text-primary);
  font-size: var(--fs-xs);
  padding: var(--sp-2) var(--sp-3);
  border-radius: var(--r-sm);
  max-width: 280px;
  z-index: var(--z-dropdown);
  pointer-events: none;
  opacity: 0;
  transform: translateY(4px);
  transition: opacity var(--dur-fast), transform var(--dur-fast);
}
.tooltip.visible { opacity: 1; transform: translateY(0); }

.popover {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-md);
  padding: var(--sp-4) var(--sp-5);
  max-width: 360px;
  z-index: var(--z-dropdown);
  /* Allowed shadow exception — popover floats above page */
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.5);
}

[data-theme="light"] .popover {
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
}
```

---

## 7. Modal / Dialog

```css
.modal-overlay {
  position: fixed; inset: 0;
  background: rgba(10, 10, 15, 0.75);
  backdrop-filter: blur(6px);
  -webkit-backdrop-filter: blur(6px);
  z-index: var(--z-overlay);
  display: grid; place-items: center;
  padding: var(--sp-4);
}
[data-theme="light"] .modal-overlay {
  background: rgba(250, 250, 250, 0.75);
}

.modal {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: var(--sp-6) var(--sp-8);
  max-width: 560px;
  width: 100%;
  z-index: var(--z-modal);
  box-shadow: 0 16px 64px rgba(0, 0, 0, 0.6);
  animation: enter var(--dur-slow) var(--ease-out);
}
[data-theme="light"] .modal {
  box-shadow: 0 16px 64px rgba(0, 0, 0, 0.2);
}

.modal-title {
  font-family: var(--font-display);
  font-size: 28px;
  letter-spacing: 0.02em;
  margin-bottom: var(--sp-3);
  color: var(--text-primary);
}

/* Mobile: bottom sheet pattern */
@media (max-width: 640px) {
  .modal-overlay { align-items: flex-end; padding: 0; }
  .modal {
    max-width: 100%;
    border-radius: var(--r-lg) var(--r-lg) 0 0;
    padding-bottom: calc(var(--sp-6) + env(safe-area-inset-bottom));
  }
}
```

---

## 8. Toast / Alert

```css
.alert {
  display: flex; gap: var(--sp-3);
  padding: var(--sp-4) var(--sp-5);
  border-radius: var(--r-md);
  border: 1px solid;
  font-size: var(--fs-sm);
}
.alert-icon  { flex: none; }
.alert-body  { flex: 1; }
.alert-title { font-weight: 500; margin-bottom: var(--sp-1); color: var(--text-primary); }
.alert-msg   { color: var(--text-secondary); }

.alert-info {
  background: rgb(var(--accent-blue-rgb) / 0.06);
  border-color: rgb(var(--accent-blue-rgb) / 0.20);
}
.alert-info .alert-icon { color: var(--accent-blue); }

.alert-success {
  background: rgb(var(--accent-green-rgb) / 0.06);
  border-color: rgb(var(--accent-green-rgb) / 0.20);
}
.alert-success .alert-icon { color: var(--accent-green); }

.alert-warning {
  background: rgb(var(--accent-yellow-rgb) / 0.06);
  border-color: rgb(var(--accent-yellow-rgb) / 0.20);
}
.alert-warning .alert-icon { color: var(--accent-yellow); }

.alert-danger {
  background: rgb(var(--accent-red-rgb) / 0.06);
  border-color: rgb(var(--accent-red-rgb) / 0.20);
}
.alert-danger .alert-icon { color: var(--accent-red); }

/* Toast — top-right floating */
.toast-stack {
  position: fixed;
  top: var(--sp-5); right: var(--sp-5);
  display: flex; flex-direction: column; gap: var(--sp-3);
  z-index: var(--z-toast);
  max-width: 380px;
}
.toast { animation: enter var(--dur-base) var(--ease-out); }
```

---

## 9. Skeleton, Empty states, Spinner

```css
/* SKELETON — content placeholder while loading */
.skeleton {
  background: linear-gradient(
    90deg,
    var(--surface) 0%,
    var(--card) 50%,
    var(--surface) 100%
  );
  background-size: 200% 100%;
  border-radius: var(--r-sm);
  animation: shimmer 1.5s linear infinite;
}
@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
.skeleton-text   { height: 1em; margin-bottom: var(--sp-2); }
.skeleton-circle { border-radius: 50%; }

/* EMPTY STATE */
.empty {
  display: flex; flex-direction: column; align-items: center;
  gap: var(--sp-3); padding: var(--sp-12);
  text-align: center;
}
.empty-icon  { color: var(--text-dim); font-size: 48px; }
.empty-title {
  font-family: var(--font-display);
  font-size: 24px;
  color: var(--text-secondary);
}
.empty-msg   {
  font-size: var(--fs-sm);
  color: var(--text-muted);
  max-width: 400px;
}

/* SPINNER — for async actions inside buttons */
.spinner {
  display: inline-block;
  width: 14px; height: 14px;
  border: 2px solid currentColor;
  border-top-color: transparent;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
```

---

## 10. Code blocks (data science / dev tutorials)

```css
.code-block {
  position: relative;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-md);
  font-family: var(--font-mono);
  font-size: var(--fs-code);
  line-height: 1.6;
  overflow-x: auto;
  margin: var(--sp-5) 0;
}
.code-block pre {
  padding: var(--sp-4) var(--sp-5);
  margin: 0;
  color: var(--text-primary);
}

/* Language tag (eyebrow style) */
.code-block::before {
  content: attr(data-lang);
  position: absolute; top: var(--sp-3); right: var(--sp-4);
  font-size: var(--fs-micro);
  letter-spacing: 0.15em;
  color: var(--text-muted);
  text-transform: uppercase;
}

/* Syntax tokens — pair with Prism.js / Highlight.js / Shiki */
.tok-keyword  { color: var(--accent-red); }
.tok-string   { color: var(--accent-green); }
.tok-number   { color: var(--accent-yellow); }
.tok-comment  { color: var(--text-dim); font-style: italic; }
.tok-function { color: var(--accent-blue); }
.tok-class    { color: var(--accent-purple); }
.tok-operator { color: var(--text-secondary); }
.tok-builtin  { color: var(--accent-cyan); }

/* Inline code */
:not(pre) > code {
  font-family: var(--font-mono);
  font-size: 0.92em;
  padding: 2px 6px;
  background: var(--hairline);
  border: 1px solid var(--border);
  border-radius: var(--r-xs);
  color: var(--accent-cyan);
}

/* Line numbers (optional) */
.code-block.with-lines pre {
  counter-reset: line;
  padding-left: var(--sp-12);
}
.code-block.with-lines .line { position: relative; }
.code-block.with-lines .line::before {
  counter-increment: line;
  content: counter(line);
  position: absolute; left: calc(-1 * var(--sp-8));
  color: var(--text-dim);
  font-size: var(--fs-xs);
}
```

---

## 11. Tabs

```css
.tabs {
  display: flex; gap: var(--sp-1);
  border-bottom: 1px solid var(--border);
  margin-bottom: var(--sp-5);
}
.tab {
  padding: var(--sp-3) var(--sp-4);
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--text-muted);
  border: none; background: none;
  cursor: pointer;
  border-bottom: 2px solid transparent;
  margin-bottom: -1px;
  transition: color var(--dur-fast), border-color var(--dur-fast);
}
.tab:hover { color: var(--text-secondary); }
.tab[aria-selected="true"] {
  color: var(--accent-green);
  border-bottom-color: var(--accent-green);
}
```

---

## 12. Stat card (KPI display) — bonus pattern

Most-requested data-dashboard component. Lives here because it's a primitive used everywhere.

```html
<div class="stat">
  <span class="stat-label">VALIDATION ACCURACY</span>
  <span class="stat-value">94.2<span class="stat-unit">%</span></span>
  <span class="stat-delta stat-delta-up">↑ 2.1pp vs baseline</span>
</div>
```

```css
.stat { display: flex; flex-direction: column; gap: var(--sp-1); }
.stat-label {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
}
.stat-value {
  font-family: var(--font-display);
  font-size: clamp(40px, 5vw, 64px);
  line-height: 1;
  color: var(--text-primary);
  font-variant-numeric: tabular-nums;
}
.stat-unit {
  font-size: 0.5em;
  color: var(--text-muted);
  margin-left: var(--sp-1);
}
.stat-delta {
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
  letter-spacing: 0.05em;
}
.stat-delta-up   { color: var(--accent-green); }
.stat-delta-down { color: var(--accent-red); }
.stat-delta-flat { color: var(--text-muted); }
```
