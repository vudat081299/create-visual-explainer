# Navigation Patterns

Navbar, sidebar, breadcrumb, pagination, mobile drawer. Read this when building multi-page sites, app shells, or any UI with persistent navigation chrome.

The DESIGN.md original spec didn't cover navigation in detail — these patterns fill that gap while staying consistent with the rest of the system.

**Quick map**: §1 Top navbar (sticky) (L11–140) · §2 Mobile drawer (L141–240) · §3 Sidebar (dashboards) (L241–351) · §4 Breadcrumb (L352–403) · §5 Pagination (L404–456) · §6 Tabs (cross-ref to components.md §11) (L457–462) · §7 Skip-link (a11y) (L463–494) · §8 Footer (marketing/docs) (L495–581)

---

## 1. Top navbar (sticky, persistent)

The default top-bar pattern: logo on left, nav links center-or-right, action buttons far right.

```html
<header class="navbar">
  <div class="navbar-inner">
    <a href="/" class="navbar-brand">
      <span class="brand-mark">⌬</span>
      <span class="brand-name">DATA<span>LAB</span></span>
    </a>

    <nav class="navbar-links">
      <a href="/dashboard" class="nav-link">Dashboard</a>
      <a href="/models"    class="nav-link" aria-current="page">Models</a>
      <a href="/data"      class="nav-link">Data</a>
      <a href="/docs"      class="nav-link">Docs</a>
    </nav>

    <div class="navbar-actions">
      <button class="btn btn-ghost btn-sm" aria-label="Search"><kbd>⌘K</kbd></button>
      <button class="btn btn-secondary btn-sm">Sign in</button>
      <button class="btn btn-primary   btn-sm">Get started</button>
    </div>

    <button class="navbar-toggle" id="nav-toggle" aria-label="Open menu" aria-expanded="false">
      <span></span><span></span><span></span>
    </button>
  </div>
</header>
```

```css
.navbar {
  position: sticky; top: 0;
  /* Translucent + blur — uses --bg-rgb from tokens.md §1.1.
     Falls back gracefully on browsers without backdrop-filter. */
  background: rgb(var(--bg-rgb) / 0.85);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
  z-index: var(--z-sticky);
}
/* If you don't need the blur effect, the solid version is fine: */
/* .navbar { background: var(--bg); } */

.navbar-inner {
  display: flex; align-items: center;
  gap: var(--sp-6);
  max-width: 1400px;
  margin-inline: auto;
  padding: var(--sp-3) var(--sp-6);
  min-height: 56px;
}

/* Brand mark — small geometric glyph + wordmark */
.navbar-brand {
  display: flex; align-items: center;
  gap: var(--sp-2);
  text-decoration: none;
  color: var(--text-primary);
}
.brand-mark {
  display: grid; place-items: center;
  width: 28px; height: 28px;
  background: var(--accent-green);
  color: var(--bg);
  border-radius: var(--r-sm);
  font-size: 18px;
  font-weight: 700;
}
.brand-name {
  font-family: var(--font-display);
  font-size: 20px;
  letter-spacing: 0.05em;
}
.brand-name span { color: var(--accent-green); }

/* Nav links */
.navbar-links {
  display: flex; gap: var(--sp-2);
  margin-left: auto;
  margin-right: auto;          /* center between brand and actions */
}
.nav-link {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--text-muted);
  text-decoration: none;
  padding: var(--sp-2) var(--sp-3);
  border-radius: var(--r-sm);
  transition: color var(--dur-fast), background var(--dur-fast);
}
.nav-link:hover { color: var(--text-primary); background: var(--hairline); }
.nav-link[aria-current="page"] {
  color: var(--accent-green);
  background: rgb(var(--accent-green-rgb) / 0.08);
}

/* Actions on the right */
.navbar-actions { display: flex; gap: var(--sp-2); }
.navbar-actions kbd {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  padding: 1px 5px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-xs);
  color: var(--text-muted);
}

/* Mobile: hide links, show toggle */
.navbar-toggle { display: none; background: none; border: none; cursor: pointer; padding: var(--sp-2); }
.navbar-toggle span {
  display: block; width: 20px; height: 1.5px;
  background: var(--text-primary);
  margin: 4px 0;
  transition: transform var(--dur-fast);
}

@media (max-width: 768px) {
  .navbar-links, .navbar-actions { display: none; }
  .navbar-toggle { display: block; margin-left: auto; }
}
```

---

## 2. Mobile drawer

Triggered by the navbar toggle. Slides in from the right.

```html
<div class="drawer" id="drawer" data-open="false">
  <div class="drawer-overlay"></div>
  <aside class="drawer-panel">
    <button class="drawer-close" aria-label="Close menu">✕</button>
    <nav class="drawer-links">
      <a href="/dashboard" class="drawer-link">Dashboard</a>
      <a href="/models"    class="drawer-link" aria-current="page">Models</a>
      <a href="/data"      class="drawer-link">Data</a>
      <a href="/docs"      class="drawer-link">Docs</a>
    </nav>
    <div class="drawer-actions">
      <button class="btn btn-secondary">Sign in</button>
      <button class="btn btn-primary">Get started</button>
    </div>
  </aside>
</div>
```

```css
.drawer {
  position: fixed; inset: 0;
  z-index: var(--z-overlay);
  pointer-events: none;
}
.drawer[data-open="true"] { pointer-events: auto; }

.drawer-overlay {
  position: absolute; inset: 0;
  background: rgba(10,10,15,0.6);
  opacity: 0;
  transition: opacity var(--dur-base) var(--ease-out);
}
.drawer[data-open="true"] .drawer-overlay { opacity: 1; }

.drawer-panel {
  position: absolute;
  top: 0; right: 0; bottom: 0;
  width: min(320px, 80vw);
  background: var(--card);
  border-left: 1px solid var(--border);
  padding: var(--sp-6);
  display: flex; flex-direction: column;
  gap: var(--sp-5);
  transform: translateX(100%);
  transition: transform var(--dur-base) var(--ease-out);
}
.drawer[data-open="true"] .drawer-panel { transform: translateX(0); }

.drawer-close {
  align-self: flex-end;
  background: none; border: none;
  color: var(--text-muted);
  font-size: 20px;
  cursor: pointer;
  padding: var(--sp-2);
}
.drawer-links { display: flex; flex-direction: column; gap: var(--sp-1); }
.drawer-link {
  font-family: var(--font-display);
  font-size: 28px;
  letter-spacing: 0.02em;
  color: var(--text-primary);
  text-decoration: none;
  padding: var(--sp-3) 0;
  border-bottom: 1px solid var(--border);
}
.drawer-link[aria-current="page"] { color: var(--accent-green); }

.drawer-actions {
  display: flex; flex-direction: column;
  gap: var(--sp-2);
  margin-top: auto;
  padding-bottom: env(safe-area-inset-bottom);
}
```

```js
const drawer = document.getElementById('drawer');
const toggle = document.getElementById('nav-toggle');
const close  = document.querySelector('.drawer-close');
const overlay= document.querySelector('.drawer-overlay');

function setOpen(v) {
  drawer.dataset.open = v;
  toggle.setAttribute('aria-expanded', v);
  document.body.style.overflow = v === 'true' ? 'hidden' : '';
}
toggle.addEventListener('click', () => setOpen('true'));
close.addEventListener('click', () => setOpen('false'));
overlay.addEventListener('click', () => setOpen('false'));
document.addEventListener('keydown', (e) => { if (e.key === 'Escape') setOpen('false'); });
```

---

## 3. Sidebar (for dashboards)

Persistent left rail with grouped sections. Common in DS dashboards / admin panels.

```html
<div class="app-shell">
  <aside class="sidebar">
    <div class="sidebar-section">
      <div class="sidebar-label">Workspace</div>
      <a href="/" class="sidebar-link"><span class="sidebar-icon">◇</span> Overview</a>
      <a href="/models" class="sidebar-link" aria-current="page">
        <span class="sidebar-icon">▣</span> Models
        <span class="badge badge-success">12</span>
      </a>
      <a href="/datasets" class="sidebar-link"><span class="sidebar-icon">▤</span> Datasets</a>
    </div>

    <div class="sidebar-section">
      <div class="sidebar-label">Analyze</div>
      <a href="/experiments" class="sidebar-link"><span class="sidebar-icon">⚗</span> Experiments</a>
      <a href="/metrics" class="sidebar-link"><span class="sidebar-icon">⌃</span> Metrics</a>
    </div>

    <div class="sidebar-footer">
      <button class="btn btn-ghost btn-sm">Collapse</button>
    </div>
  </aside>

  <main class="app-content">
    <!-- page content -->
  </main>
</div>
```

```css
.app-shell {
  display: grid;
  grid-template-columns: 240px 1fr;
  min-height: 100vh;
}

.sidebar {
  background: var(--surface);
  border-right: 1px solid var(--border);
  padding: var(--sp-5) var(--sp-3);
  display: flex; flex-direction: column;
  gap: var(--sp-6);
  position: sticky; top: 0;
  height: 100vh;
  overflow-y: auto;
}

.sidebar-section { display: flex; flex-direction: column; gap: var(--sp-1); }

.sidebar-label {
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--text-dim);
  padding: var(--sp-2) var(--sp-3);
}

.sidebar-link {
  display: flex; align-items: center; gap: var(--sp-3);
  padding: var(--sp-2) var(--sp-3);
  color: var(--text-secondary);
  font-size: var(--fs-sm);
  text-decoration: none;
  border-radius: var(--r-sm);
  transition: color var(--dur-fast), background var(--dur-fast);
}
.sidebar-link:hover {
  color: var(--text-primary);
  background: var(--hairline);
}
.sidebar-link[aria-current="page"] {
  color: var(--accent-green);
  background: rgb(var(--accent-green-rgb) / 0.08);
}
.sidebar-link .badge { margin-left: auto; }
.sidebar-icon {
  display: inline-grid; place-items: center;
  width: 18px; height: 18px;
  font-size: 14px;
  color: var(--text-dim);
}
.sidebar-link[aria-current="page"] .sidebar-icon { color: var(--accent-green); }

.sidebar-footer { margin-top: auto; padding-top: var(--sp-3); border-top: 1px solid var(--border); }

.app-content {
  padding: var(--sp-8) var(--sp-12);
  background: var(--bg);
}

/* Collapsed state */
.app-shell[data-sidebar="collapsed"] { grid-template-columns: 64px 1fr; }
.app-shell[data-sidebar="collapsed"] .sidebar-link span:not(.sidebar-icon) { display: none; }
.app-shell[data-sidebar="collapsed"] .sidebar-label { display: none; }

/* Mobile: hide sidebar; switch to drawer pattern (§2) */
@media (max-width: 1024px) {
  .app-shell { grid-template-columns: 1fr; }
  .sidebar { display: none; }
  .app-content { padding: var(--sp-5); }
}
```

---

## 4. Breadcrumb

Use mono uppercase for the trail; the current page in primary text.

```html
<nav class="breadcrumb" aria-label="Breadcrumb">
  <ol>
    <li><a href="/">Home</a></li>
    <li><a href="/models">Models</a></li>
    <li><a href="/models/llms">LLMs</a></li>
    <li aria-current="page">Mistral 7B</li>
  </ol>
</nav>
```

```css
.breadcrumb ol {
  display: flex; flex-wrap: wrap;
  align-items: center;
  gap: var(--sp-2);
  list-style: none;
  padding: 0; margin: 0;
}
.breadcrumb li {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
}
.breadcrumb li + li::before {
  content: '/';
  margin-right: var(--sp-2);
  color: var(--text-dim);
}
.breadcrumb a {
  color: var(--text-muted);
  text-decoration: none;
  transition: color var(--dur-fast);
}
.breadcrumb a:hover { color: var(--text-secondary); }
.breadcrumb [aria-current="page"] {
  color: var(--accent-green);
  font-family: var(--font-body);
  font-size: var(--fs-sm);
  letter-spacing: 0.02em;
  text-transform: none;
}
```

---

## 5. Pagination

```html
<nav class="pagination" aria-label="Pagination">
  <button class="page-btn" disabled aria-label="Previous">←</button>
  <button class="page-btn">1</button>
  <button class="page-btn" aria-current="page">2</button>
  <button class="page-btn">3</button>
  <span class="page-ellipsis">…</span>
  <button class="page-btn">42</button>
  <button class="page-btn" aria-label="Next">→</button>
</nav>
```

```css
.pagination {
  display: flex; gap: var(--sp-1);
  align-items: center;
  justify-content: center;
  margin: var(--sp-8) 0;
}
.page-btn {
  font-family: var(--font-mono);
  font-size: var(--fs-sm);
  font-variant-numeric: tabular-nums;
  min-width: 36px; height: 36px;
  padding: 0 var(--sp-2);
  background: transparent;
  border: 1px solid transparent;
  color: var(--text-muted);
  border-radius: var(--r-sm);
  cursor: pointer;
  transition: all var(--dur-fast) var(--ease-out);
}
.page-btn:hover:not(:disabled):not([aria-current]) {
  border-color: var(--border);
  color: var(--text-primary);
}
.page-btn[aria-current="page"] {
  background: var(--accent-green);
  color: var(--bg);
  border-color: var(--accent-green);
}
.page-btn:disabled { opacity: 0.3; cursor: not-allowed; }
.page-ellipsis {
  font-family: var(--font-mono);
  color: var(--text-dim);
  padding: 0 var(--sp-2);
}
```

---

## 6. Tabs (already in components.md §11)

Cross-reference only. Use the `.tabs` / `.tab` pattern from `components/core.md` for in-page section switching. Do not confuse with navbar links — tabs are scoped to a single page.

---

## 7. Skip-link (accessibility)

Hidden by default, visible on `:focus`. Required for keyboard/screen-reader users.

```html
<a href="#main" class="skip-link">Skip to main content</a>
<!-- ... navbar etc ... -->
<main id="main">...</main>
```

```css
.skip-link {
  position: absolute;
  top: 0; left: 0;
  padding: var(--sp-3) var(--sp-4);
  background: var(--accent-yellow);
  color: var(--bg);
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  text-decoration: none;
  border-radius: 0 0 var(--r-sm) 0;
  transform: translateY(-100%);
  z-index: var(--z-toast);
  transition: transform var(--dur-fast);
}
.skip-link:focus-visible { transform: translateY(0); }
```

---

## 8. Footer (for marketing/docs pages)

```html
<footer class="site-footer">
  <div class="container">
    <div class="footer-grid">
      <div class="footer-brand">
        <span class="brand-mark">⌬</span>
        <span class="brand-name">DATA<span>LAB</span></span>
        <p class="footer-tagline">Industrial-strength tooling for ML practitioners.</p>
      </div>

      <div class="footer-col">
        <div class="footer-label">Product</div>
        <a href="/models">Models</a>
        <a href="/datasets">Datasets</a>
        <a href="/pricing">Pricing</a>
      </div>

      <div class="footer-col">
        <div class="footer-label">Resources</div>
        <a href="/docs">Documentation</a>
        <a href="/blog">Blog</a>
        <a href="/changelog">Changelog</a>
      </div>
    </div>

    <div class="footer-bottom">
      <span>© 2026 Datalab Inc.</span>
      <span>v1.4.2 · build a8c12f</span>
    </div>
  </div>
</footer>
```

```css
.site-footer {
  border-top: 1px solid var(--border);
  background: var(--surface);
  padding: var(--sp-12) 0 var(--sp-6);
  margin-top: var(--sp-20);
}
.footer-grid {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr 1fr;
  gap: var(--sp-8);
}
.footer-tagline {
  font-size: var(--fs-sm);
  color: var(--text-muted);
  margin-top: var(--sp-3);
  max-width: 280px;
}
.footer-col { display: flex; flex-direction: column; gap: var(--sp-2); }
.footer-label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-secondary);
  margin-bottom: var(--sp-2);
}
.footer-col a {
  font-size: var(--fs-sm);
  color: var(--text-muted);
  text-decoration: none;
}
.footer-col a:hover { color: var(--text-primary); }

.footer-bottom {
  display: flex; justify-content: space-between;
  font-family: var(--font-mono);
  font-size: var(--fs-micro);
  letter-spacing: 0.1em;
  color: var(--text-dim);
  text-transform: uppercase;
  padding-top: var(--sp-6);
  margin-top: var(--sp-8);
  border-top: 1px solid var(--border);
}

@media (max-width: 768px) {
  .footer-grid { grid-template-columns: 1fr 1fr; }
  .footer-brand { grid-column: 1 / -1; }
}
```
