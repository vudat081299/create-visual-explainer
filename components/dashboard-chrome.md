# Dashboard Chrome Patterns

UI chrome that surrounds dashboard data: segmented controls for switching views, filter chips for active filters, banners for page-level notices, avatars for authorship and identity. Read this when building admin/analytics UIs that need these chrome primitives — usually loaded with `components/numeric.md` and `components/charts.md` for full dashboard work.

For the *numeric primitives* (progress, meter, sparkline, count-up, stat-grid), see `components/numeric.md`. For full charts, see `components/charts.md`. For the basic `.alert` and inline alerts, see `components/core.md` §8 — the banner here is full-width and less prominent.

**Quick map**: §1 Segmented control (L11–73) · §2 Filter chip (L74–147) · §3 Banner (L148–193) · §4 Avatar / Initials marker (L194–261)

---

## 1. Segmented control (period picker, mode toggle)

Like tabs, but visually a single connected pill — used for **mutually exclusive view modes** that don't change the page structure (just the data shown).

```html
<div class="segmented" role="radiogroup" aria-label="Time range">
  <button class="segment" role="radio" aria-checked="false">1D</button>
  <button class="segment" role="radio" aria-checked="false">1W</button>
  <button class="segment" role="radio" aria-checked="true">1M</button>
  <button class="segment" role="radio" aria-checked="false">1Y</button>
  <button class="segment" role="radio" aria-checked="false">All</button>
</div>
```

```css
.segmented {
  display: inline-flex;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-sm);
  padding: 2px;
}
.segment {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text-muted);
  background: transparent;
  border: none;
  padding: var(--sp-2) var(--sp-4);
  border-radius: 4px;
  cursor: pointer;
  transition: background var(--dur-fast), color var(--dur-fast);
}
.segment:hover { color: var(--text-primary); }
.segment[aria-checked="true"] {
  background: var(--card);
  color: var(--accent-green);
  /* Inset look — visual press */
  box-shadow: inset 0 0 0 1px var(--border);
}
.segment:focus-visible {
  outline: 2px solid var(--accent-green);
  outline-offset: 2px;
}
```

```js
document.querySelectorAll('.segmented').forEach(group => {
  group.querySelectorAll('.segment').forEach(seg => {
    seg.addEventListener('click', () => {
      group.querySelectorAll('.segment').forEach(s => s.setAttribute('aria-checked', 'false'));
      seg.setAttribute('aria-checked', 'true');
      // dispatch a custom event so the page can react
      group.dispatchEvent(new CustomEvent('change', { detail: { value: seg.textContent } }));
    });
  });
});
```

---

## 2. Filter chip (with remove button)

Active-filter representation. Used in dashboards to show the current query state.

```html
<div class="chip-row" aria-label="Active filters">
  <span class="chip-row-label">FILTERS</span>
  <span class="chip">
    Status: <strong>Running</strong>
    <button class="chip-close" aria-label="Remove Status filter">✕</button>
  </span>
  <span class="chip">
    Model: <strong>Mistral 7B</strong>
    <button class="chip-close" aria-label="Remove Model filter">✕</button>
  </span>
  <button class="chip-clear">Clear all</button>
</div>
```

```css
.chip-row {
  display: flex; flex-wrap: wrap; align-items: center;
  gap: var(--sp-2);
  margin: var(--sp-3) 0;
}
.chip-row-label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin-right: var(--sp-2);
}
.chip {
  display: inline-flex; align-items: center;
  gap: var(--sp-2);
  font-family: var(--font-body);
  font-size: var(--fs-xs);
  padding: 4px var(--sp-2) 4px var(--sp-3);
  background: rgb(var(--accent-cyan-rgb) / 0.08);
  color: var(--accent-cyan);
  border: 1px solid rgb(var(--accent-cyan-rgb) / 0.22);
  border-radius: var(--r-pill);
}
.chip strong { font-weight: 500; color: var(--text-primary); }
.chip-close {
  background: transparent;
  border: none;
  color: var(--accent-cyan);
  font-size: var(--fs-xs);
  width: 16px; height: 16px;
  display: grid; place-items: center;
  border-radius: 50%;
  cursor: pointer;
  padding: 0;
}
.chip-close:hover { background: rgb(var(--accent-cyan-rgb) / 0.18); }

.chip-clear {
  background: transparent;
  border: none;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text-muted);
  cursor: pointer;
  padding: var(--sp-1) var(--sp-2);
}
.chip-clear:hover { color: var(--accent-red); }
```

---

## 3. Banner (announcement / inline alert)

Page-wide notice strip. Different from `.alert` (in `components/core.md` §8): a banner is full-width, less visually prominent, often dismissible.

```html
<div class="banner" data-variant="info">
  <span class="banner-icon" aria-hidden="true">ⓘ</span>
  <p class="banner-msg">
    <strong>Updated 2026-05-04.</strong> This article was revised to include heavy-ball momentum (§5).
  </p>
  <button class="banner-close" aria-label="Dismiss">✕</button>
</div>
```

```css
.banner {
  display: flex; align-items: center; gap: var(--sp-3);
  padding: var(--sp-3) var(--sp-5);
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  font-size: var(--fs-sm);
  color: var(--text-secondary);
}
.banner-icon { color: var(--accent-blue); flex: none; }
.banner-msg  { flex: 1; margin: 0; }
.banner-msg strong { color: var(--text-primary); font-weight: 500; }
.banner-close {
  background: transparent; border: none;
  color: var(--text-muted);
  cursor: pointer;
  padding: var(--sp-1) var(--sp-2);
  border-radius: var(--r-xs);
}
.banner-close:hover { background: var(--hairline); color: var(--text-primary); }

/* Variants — left-border accent identifies severity */
.banner[data-variant="info"]    { border-left: 3px solid var(--accent-blue); }
.banner[data-variant="success"] { border-left: 3px solid var(--accent-green); }
.banner[data-variant="warning"] { border-left: 3px solid var(--accent-yellow); }
.banner[data-variant="warning"] .banner-icon { color: var(--accent-yellow); }
.banner[data-variant="danger"]  { border-left: 3px solid var(--accent-red); }
.banner[data-variant="danger"]  .banner-icon { color: var(--accent-red); }
```

---

## 4. Avatar / Initials marker

For author bylines, instructor profiles, or commenter identification. Defaults to colored initials when no image.

```html
<!-- Image avatar -->
<div class="avatar"><img src="/dat.jpg" alt="Đạt" /></div>

<!-- Initials avatar -->
<div class="avatar avatar-initials" data-color="green">
  <span aria-hidden="true">DT</span>
</div>

<!-- With name -->
<div class="byline">
  <div class="avatar avatar-initials" data-color="cyan"><span>DT</span></div>
  <div>
    <div class="byline-name">Đạt</div>
    <div class="byline-role">Author · 2026-05-04</div>
  </div>
</div>
```

```css
.avatar {
  width: 36px; height: 36px;
  border-radius: 50%;
  overflow: hidden;
  display: grid; place-items: center;
  background: var(--surface);
  border: 1px solid var(--border);
  flex: none;
}
.avatar img { width: 100%; height: 100%; object-fit: cover; }
.avatar-initials span {
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
  font-weight: 500;
  letter-spacing: 0.05em;
}
.avatar-initials[data-color="green"]  { background: rgb(var(--accent-green-rgb) / 0.10); }
.avatar-initials[data-color="green"]  span { color: var(--accent-green); }
.avatar-initials[data-color="blue"]   { background: rgb(var(--accent-blue-rgb) / 0.10); }
.avatar-initials[data-color="blue"]   span { color: var(--accent-blue); }
.avatar-initials[data-color="cyan"]   { background: rgb(var(--accent-cyan-rgb) / 0.10); }
.avatar-initials[data-color="cyan"]   span { color: var(--accent-cyan); }
.avatar-initials[data-color="purple"] { background: rgb(var(--accent-purple-rgb) / 0.10); }
.avatar-initials[data-color="purple"] span { color: var(--accent-purple); }

/* Sizes */
.avatar-sm { width: 24px; height: 24px; }
.avatar-sm .avatar-initials span { font-size: var(--fs-nano); }
.avatar-lg { width: 56px; height: 56px; }
.avatar-lg .avatar-initials span { font-size: var(--fs-base); }

.byline {
  display: flex; align-items: center; gap: var(--sp-3);
}
.byline-name { font-size: var(--fs-sm); color: var(--text-primary); }
.byline-role {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text-muted);
}
```
