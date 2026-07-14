# Progressive Disclosure — overview-first + brief/expand (right detail panel)

A reading-layout pattern for dense learning pages: give the reader a **map and the gist first**, keep depth one click away in a **right-hand detail panel**. This is the spatial form of rule 11 (layered explanation) and rule 13 (comprehension-first): the *brief* carries Layer 1–2 inline, the *panel* carries Layer 3 + everything heavy.

Use this when the page must contain a lot of knowledge but the reader needs to skim the whole argument first and dive into any one point on demand. Extends `usecases/longform-study.md` (TOC + anchors) and reuses the accordion from `components/asides.md` (mobile fallback) and the close-affordances from `components/core.md` (modal/overlay). For *interactive* depth (sliders, sandbox) the panel can host an `usecases/interactive-explainer.md` stage.

**Quick map**: §1 Principle (L11–26) · §2 The layering law — brief vs expand (L28–48) · §3 Page-overview block / advance organizer (L50–100) · §4 Brief + right detail panel + drag-to-resize (L102–322) · §5 Responsive fallback ≤1024px + print/no-JS (L324–351) · §6 Accessibility & motion (L353–366) · §7 Two-tier reading modes — simple/detail tabs (L370–399)

---

## 1. Principle

Two ideas, both from the pedagogy lineage (Distill, 3Blue1Brown, Brilliant):

- **Advance organizer** — before the first concept, give the reader the *big picture* and a *map* of how the pieces connect. A reader who knows where the path goes reads everything else faster.
- **Progressive disclosure** — show the minimum that lets the reader follow the argument; reveal depth only when they ask for it. Depth that is always-on is noise for the skimmer and a wall for the beginner.

The layout has three zones:

1. **Page-overview** at the very top — the advance organizer (§3).
2. **Brief** per concept, always visible in the main column — Layer 1 (intuition) + Layer 2 (mechanism) + the one key formula (§2, §4).
3. **Detail panel**, slides in from the right on demand — Layer 3 and all heavy material (§4).

Do **not** confuse this with `components/asides.md` collapsibles. A collapsible hides an *aside* inline; this pattern moves *the entire depth tier* to a dedicated panel so the main column stays a clean, skimmable spine.

---

## 2. The layering law — brief vs expand

This is the part that makes or breaks the pattern. Without an explicit rule, depth leaks into the brief (page bloats) or the brief gets gutted (main column reads empty). Bind the split to the pedagogy layers:

**Brief (default, always on screen)**
- Layer 1 (cartoon / analogy / the question this answers) + Layer 2 (how it works in plain words) + **exactly one** key formula or definition.
- Budget: ≈120 words, ≤1 formula, ≤1 small inline figure.
- **Self-sufficiency test (hard rule):** a reader who reads *only the briefs*, top to bottom, opening nothing, must still follow the page's whole argument. If understanding the next brief *requires* opening a panel, that brief is under-filled — pull the needed piece up.

**Expand (right panel, on demand)**
- Layer 3 (deriving the formula, the algebra), full worked numerical examples, edge cases, proofs, code, long reference tables, "what to read next".
- **Anti-leak rule (inverse of the self-sufficiency test):** never put anything in the panel whose absence makes the brief *wrong* or *unfollowable*. The panel only goes **deeper**, never makes the brief **complete**. If removing a panel breaks a brief, that content was misfiled.
- **Don't-gut rule (block level):** when you disclose *individual* blocks rather than a whole concept, never move a concept's *only* substantive content into the panel. If opening the trigger is the only way to see anything past the heading, that block belongs inline — hiding it is not disclosure (same spirit as never hiding the core teaching visual).

**Teaser line** — end every brief with one `.teaser` line naming what the panel holds (3–5 words per item), so the reader decides whether to open it. Example: *"Mở rộng: suy ra mẫu số · ví dụ số · edge case NaN · code."*

**Relationship to §2C / §2D.** Per §2D this brief/expand split is the **default** reading layout for a committed multi-concept page — you do not ask the user before using it. §2C is separate: it gates *whether optional extras exist at all* (quiz, footnotes, vocab tooltip, interactive code playground, >1 worked example, A/B comparison) — those stay opt-in. So the split is default; the optional extras are still gated. Code listings, Layer-3 derivations, supplementary diagrams and edge cases the page already commits to all go in the panel by default.

**Relationship to rules 11–13.** The brief *is* Layer 1–2; the panel *is* Layer 3. This usecase doesn't override `foundations/pedagogy.md` — it gives the layers a fixed place on the page.

---

## 3. Page-overview block (advance organizer)

Sits at the top of the article, before the first concept. Keep it compact — a lead paragraph that states the destination, plus a map of the sections. The map links into the page; it is *not* the TOC rail (the rail is for jumping while deep in the page — this is for orienting before starting).

### HTML

```html
<section class="page-overview" id="overview">
  <span class="eyebrow">BẮT ĐẦU TỪ ĐÂY</span>
  <h1>Bức tranh lớn</h1>
  <p class="overview-lead">
    Hai–ba câu nói rõ trang này đưa người đọc từ đâu đến đâu, và mạch xuyên suốt là gì.
  </p>
  <ol class="overview-map">
    <li><a href="#c-part1"><span class="map-k">I</span> Tên phần — một câu vai trò</a></li>
    <li><a href="#c-part2"><span class="map-k">II</span> Tên phần — một câu vai trò</a></li>
    <li><a href="#c-part3"><span class="map-k">III</span> Tên phần — một câu vai trò</a></li>
  </ol>
</section>
```

### CSS

```css
.page-overview {
  padding: var(--sp-8) 0 var(--sp-12);
  border-bottom: 1px solid var(--border);
  margin-bottom: var(--sp-12);
}
.overview-lead {
  font-size: var(--fs-lg);
  line-height: 1.7;
  color: var(--text-secondary);
  max-width: 60ch;
}
.overview-map { list-style: none; padding: 0; margin: var(--sp-6) 0 0; display: grid; gap: var(--sp-2); }
.overview-map a {
  display: flex; gap: var(--sp-3); align-items: baseline;
  padding: var(--sp-2) var(--sp-3);
  border-left: 2px solid transparent;
  color: var(--text-muted); text-decoration: none;
  border-radius: var(--r-sm);
  transition: color var(--dur-fast), border-color var(--dur-fast), background var(--dur-fast);
}
.overview-map a:hover { color: var(--text-primary); border-left-color: var(--accent-identity); background: color-mix(in srgb, var(--accent-identity) 6%, transparent); }
.map-k { font-family: var(--font-mono); font-size: var(--fs-sm); color: var(--accent-identity); font-variant-numeric: tabular-nums; min-width: 2.2em; }
```

Optional: a small hand-drawn inline `<svg>` showing the section arc (boxes + arrows) above or beside the map. Keep it decorative-light and give it a `<figcaption>`. Per §2C this SVG is optional unless the user asked for a visual map.

---

## 4. Brief + right detail panel

Each concept is a `<section class="concept">` carrying the brief + a trigger. The depth lives in a hidden source block next to the concept (so KaTeX auto-render processes it at load while it is `display:none`), and a single persistent panel clones that source in on open. One panel, one open at a time.

### HTML — per concept + the single panel

```html
<!-- one of these per concept, inside <article> -->
<section class="concept" id="c-mase">
  <h2 id="mase">MASE</h2>

  <!-- BRIEF: Layer 1 + 2 + the one key formula -->
  <p>Trực giác + cơ chế bằng lời thường, gọn.</p>
  <p>$$\text{MASE} = \frac{\text{MAE}_\text{model}}{\text{MAE}^\text{train}_\text{SNaive}}$$</p>

  <p class="teaser">Mở rộng: suy ra mẫu số · ví dụ số chạy tay · edge case NaN · code.</p>
  <button class="expand-btn" data-expand="mase" aria-expanded="false">Mở rộng chi tiết &rarr;</button>

  <!-- DEPTH SOURCE: hidden, pre-rendered by KaTeX at load, cloned into panel on open -->
  <div class="detail-source" id="detail-mase" data-title="MASE — chi tiết">
    <h3>Suy ra mẫu số</h3>
    <p>Layer 3, ví dụ số, edge case, code... (escape &lt; &gt; &amp; trong &lt;pre&gt;&lt;code&gt;).</p>
  </div>
</section>

<!-- ONE persistent panel for the whole page, placed at end of body -->
<div class="detail-backdrop" hidden></div>
<aside class="detail-panel" id="detail-panel" role="dialog" aria-modal="false" aria-label="Chi tiết">
  <div class="detail-resize" role="separator" aria-orientation="vertical" aria-label="Kéo để đổi độ rộng" tabindex="0"></div>
  <header class="detail-head">
    <span class="detail-title"></span>
    <button class="detail-close" aria-label="Đóng">&times;</button>
  </header>
  <div class="detail-body"></div>
</aside>
```

**Multiple triggers in one concept (block-level disclosure).** A concept isn't limited to one trigger. Give each heavy block its own `data-expand` + `.detail-source`; the single page-level panel clones whichever source you click, still one-open-at-a-time. This is how you disclose code *and* a template *and* a reference table separately inside an already-detailed section — the reader is past the brief, but each heavy block still opens on demand instead of being dumped inline:

```html
<button class="expand-btn" data-expand="dm-code"  aria-expanded="false">Mở code Python &rarr;</button>
<div class="detail-source" id="detail-dm-code"  data-title="DM — code">…</div>

<button class="expand-btn" data-expand="dm-learn" aria-expanded="false">Cần học thêm &rarr;</button>
<div class="detail-source" id="detail-dm-learn" data-title="DM — cần học thêm">…</div>
```

Convention (matches the JS below): `data-expand="X"` opens `#detail-X`, so every `data-expand` suffix and its source `id` must be unique across the page. Keep essential prose, derivations and worked numbers inline; disclose only the code / supplementary / reference blocks — and never the concept's *only* content (§2 don't-gut rule).

### CSS

```css
.detail-source { display: none; }   /* KaTeX still traverses it at load */

.expand-btn {
  font-family: var(--font-mono); font-size: var(--fs-sm);
  color: var(--accent-identity);
  background: color-mix(in srgb, var(--accent-identity) 9%, transparent);
  border: 1px solid color-mix(in srgb, var(--accent-identity) 22%, transparent);  /* triple-opacity */
  border-radius: var(--r-pill);
  padding: var(--sp-2) var(--sp-4);
  cursor: pointer;
  transition: background var(--dur-fast), transform var(--dur-fast);
}
.expand-btn:hover { background: color-mix(in srgb, var(--accent-identity) 14%, transparent); }
.expand-btn[aria-expanded="true"] { background: color-mix(in srgb, var(--accent-identity) 16%, transparent); }
.teaser { font-size: var(--fs-sm); color: var(--text-muted); margin-top: var(--sp-2); }

.detail-panel {
  --panel-w: min(560px, 92vw);                    /* default; JS overwrites when user drags */
  position: fixed; top: 0; right: 0; height: 100vh;
  width: var(--panel-w);
  display: flex; flex-direction: column;
  background: var(--card);
  border-left: 1px solid var(--border);
  transform: translateX(100%);                    /* animate transform only (NOT width) */
  transition: transform var(--dur-med) var(--ease-out);
  z-index: var(--z-modal);
  will-change: transform;
}
.detail-panel.open { transform: translateX(0); box-shadow: -24px 0 48px rgba(0,0,0,.35); } /* shadow = true overlay */
.detail-panel.resizing { transition: none; user-select: none; }  /* no transition while dragging width */

/* drag handle on the LEFT edge of the panel */
.detail-resize {
  position: absolute; top: 0; left: 0; height: 100%;
  width: 10px; margin-left: -5px;                 /* straddle the edge for an easy grab */
  cursor: col-resize;
  z-index: 1;
  touch-action: none;                             /* let JS own the touch gesture */
}
.detail-resize::after {                           /* thin visible grip, brightens on hover/drag */
  content: ""; position: absolute; top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  width: 2px; height: 36px; border-radius: var(--r-pill);
  background: var(--border);
  transition: background var(--dur-fast);
}
.detail-resize:hover::after,
.detail-panel.resizing .detail-resize::after { background: var(--accent-identity); }
.detail-head {
  display: flex; align-items: center; justify-content: space-between;
  padding: var(--sp-5) var(--sp-6); border-bottom: 1px solid var(--border);
  flex: 0 0 auto;
}
.detail-title { font-family: var(--font-mono); font-size: var(--fs-label); letter-spacing: .14em; text-transform: uppercase; color: var(--text-muted); }
.detail-close { background: none; border: none; color: var(--text-muted); font-size: 1.5rem; line-height: 1; cursor: pointer; padding: var(--sp-1) var(--sp-2); border-radius: var(--r-sm); }
.detail-close:hover { color: var(--text-primary); }
.detail-body { overflow-y: auto; padding: var(--sp-6); }

.detail-backdrop { position: fixed; inset: 0; z-index: calc(var(--z-modal) - 1); background: transparent; }
body.detail-open .detail-backdrop { display: block; }   /* desktop: transparent, still catches outside-click */
```

### JS — open/close, one-at-a-time, focus handling

```js
(function detailPanel() {
  const panel = document.getElementById('detail-panel');
  if (!panel) return;
  const body = panel.querySelector('.detail-body');
  const titleEl = panel.querySelector('.detail-title');
  let lastTrigger = null;

  function open(id, trigger) {
    const src = document.getElementById('detail-' + id);
    if (!src) return;
    titleEl.textContent = src.dataset.title || '';
    body.replaceChildren(...Array.from(src.cloneNode(true).childNodes)); // KaTeX already rendered in source
    document.querySelectorAll('.expand-btn[aria-expanded="true"]').forEach(b => b.setAttribute('aria-expanded', 'false'));
    if (trigger) trigger.setAttribute('aria-expanded', 'true');
    panel.classList.add('open');
    document.body.classList.add('detail-open');
    body.scrollTop = 0;
    lastTrigger = trigger || null;
    panel.querySelector('.detail-close').focus();   // move focus into panel
  }
  function close() {
    panel.classList.remove('open');
    document.body.classList.remove('detail-open');
    document.querySelectorAll('.expand-btn[aria-expanded="true"]').forEach(b => b.setAttribute('aria-expanded', 'false'));
    if (lastTrigger) { lastTrigger.focus(); lastTrigger = null; }  // return focus to trigger
  }
  document.addEventListener('click', (e) => {
    const t = e.target.closest('[data-expand]');
    if (t) { e.preventDefault(); open(t.getAttribute('data-expand'), t); return; }
    if (e.target.closest('.detail-close') || e.target.classList.contains('detail-backdrop')) close();
  });
  document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape' && panel.classList.contains('open')) close();
  });
})();
```

### JS — drag to resize the panel width

Width is the one property that legitimately changes here, so it is set **directly** during the drag (no CSS transition is running, so rule 9 isn't violated — the transition is only on `transform` for open/close). The chosen width is remembered in a JS variable for the session (no `localStorage` — claude.ai artifacts block it).

```js
(function panelResize() {
  const panel = document.getElementById('detail-panel');
  if (!panel) return;
  const handle = panel.querySelector('.detail-resize');
  if (!handle) return;

  const MIN = 360;                                   // px
  const max = () => Math.round(window.innerWidth * 0.90);
  let remembered = null;                             // session memory of user's chosen width
  let startX = 0, startW = 0, dragging = false;

  const clamp = (w) => Math.max(MIN, Math.min(max(), w));
  function apply(w) { panel.style.setProperty('--panel-w', clamp(w) + 'px'); }

  function down(clientX) {
    if (window.innerWidth <= 1024) return;           // full-screen sheet on mobile — no resize
    dragging = true;
    startX = clientX;
    startW = panel.getBoundingClientRect().width;
    panel.classList.add('resizing');                 // kills transition while dragging
  }
  function move(clientX) {
    if (!dragging) return;
    apply(startW + (startX - clientX));              // panel is right-anchored: drag left = wider
  }
  function up() {
    if (!dragging) return;
    dragging = false;
    panel.classList.remove('resizing');
    remembered = panel.style.getPropertyValue('--panel-w'); // keep for next open this session
  }

  handle.addEventListener('mousedown', (e) => { e.preventDefault(); down(e.clientX); });
  window.addEventListener('mousemove', (e) => move(e.clientX));
  window.addEventListener('mouseup', up);
  handle.addEventListener('touchstart', (e) => down(e.touches[0].clientX), { passive: true });
  window.addEventListener('touchmove', (e) => { if (dragging) { e.preventDefault(); move(e.touches[0].clientX); } }, { passive: false });
  window.addEventListener('touchend', up);

  // keyboard: ←/→ on the focused handle nudges width by 24px
  handle.addEventListener('keydown', (e) => {
    if (window.innerWidth <= 1024) return;
    const cur = panel.getBoundingClientRect().width;
    if (e.key === 'ArrowLeft')  { e.preventDefault(); apply(cur + 24); remembered = panel.style.getPropertyValue('--panel-w'); }
    if (e.key === 'ArrowRight') { e.preventDefault(); apply(cur - 24); remembered = panel.style.getPropertyValue('--panel-w'); }
  });

  // double-click the handle to reset to default
  handle.addEventListener('dblclick', () => { panel.style.removeProperty('--panel-w'); remembered = null; });

  // re-apply the remembered width whenever the panel re-opens (detailPanel adds .open)
  new MutationObserver(() => {
    if (panel.classList.contains('open') && remembered && window.innerWidth > 1024) {
      panel.style.setProperty('--panel-w', remembered);
    }
  }).observe(panel, { attributes: true, attributeFilter: ['class'] });
})();
```

Why clone a hidden source instead of a `<template>`: KaTeX `auto-render` walks `document.body` at load and renders math inside `display:none` nodes, but it does **not** descend into `<template>` content (an inert fragment). So put depth in a `display:none` `<div class="detail-source">`, let it render once, then clone the already-rendered markup into the panel — no re-render, math is correct on first open.

---

## 5. Responsive fallback (≤1024px)

The same panel becomes a full-screen sheet — same content, smaller screen. No separate markup; just widen to the viewport and turn on a dimmed backdrop so it reads as a true overlay.

```css
@media (max-width: 1024px) {
  .detail-panel { width: 100vw; border-left: none; }
  .detail-resize { display: none; }                      /* full-screen sheet — nothing to resize */
  body.detail-open { overflow: hidden; }                 /* lock background scroll under the sheet */
  body.detail-open .detail-backdrop { background: rgba(0,0,0,.6); }
}
```

Alternative for very small / no-JS-friendly contexts: instead of a sheet, render each depth source as an inline `<details>` accordion (see `components/asides.md`) directly under its brief. Pick one approach per page — sheet (default, matches desktop) or inline accordion (simplest). Do not ship both for the same concept. **Per §2D, a single-concept page uses the inline `<details>` accordion as its primary mechanism — no right rail; the right detail panel is for multi-concept pages.**

**Print & no-JS — reveal the hidden sources.** `.detail-source { display:none }` means that on paper (or if JS never runs) every disclosed block silently vanishes — code, templates and reference tables would be *missing* from a printout. Always add a print rule that bungs the sources back inline and hides the panel chrome:

```css
@media print {
  .detail-source { display: block !important; }   /* code / supplementary / tables print inline */
  .expand-btn { display: none !important; }        /* triggers are useless on paper */
  .detail-panel, .detail-backdrop { display: none !important; }
}
```

This keeps the printed / no-JS document complete while the on-screen page stays tidy.

---

## 6. Accessibility & motion

- **Focus** — on open, move focus to `.detail-close`; on close, return it to the trigger (the JS above does this). Keeps keyboard users from losing their place.
- **Roles** — panel is `role="dialog" aria-modal="false"` (it's a non-blocking detail rail on desktop). If you use the dimmed full-screen sheet as a blocking modal on mobile, set `aria-modal="true"` while open.
- **`aria-expanded`** on each trigger reflects whether *its* panel is showing (mirrors `components/asides.md` accordion semantics).
- **Resize handle** — it's a focusable `role="separator"` with `aria-orientation="vertical"`; `←`/`→` nudge the width 24px, double-click resets to default. Width changes are direct (no transition) so they don't violate rule 9.
- **Motion** — only `transform`/`opacity` animate (rule 9). Respect reduced motion:

```css
@media (prefers-reduced-motion: reduce) { .detail-panel { transition: none; } }
```

- **Tabular numerals** in any numeric content inside the panel (worked examples, tables) — rule 3.
- **One panel open at a time** is intentional: it keeps a single reading focus and avoids stacking overlays.

---

## 7. Two-tier reading modes (① Đơn giản / ② Chi tiết tabs)

The outermost disclosure grain. When the *whole page* has two audiences — a reader who wants the argument, and the same reader on a second pass who wants derivations, worked numbers, edge cases and code — split the page into two tab panels rather than making the briefs carry both.

```html
<div class="tabbar" role="tablist" aria-label="Độ sâu nội dung">
  <button class="tab" id="tab-btn-simple" role="tab" aria-selected="true"
          aria-controls="tab-simple" data-tab="simple"><span class="tnum">①</span> Đơn giản</button>
  <button class="tab" id="tab-btn-detail" role="tab" aria-selected="false"
          aria-controls="tab-detail" data-tab="detail"><span class="tnum">②</span> Chi tiết</button>
</div>

<section class="tab-panel active" id="tab-simple" role="tabpanel" aria-labelledby="tab-btn-simple">
  <article class="concept" data-concept="r1"> … brief: Layer 1–2 + one formula + the essential visual … </article>
</section>
<section class="tab-panel" id="tab-detail" role="tabpanel" aria-labelledby="tab-btn-detail">
  <article class="concept" data-concept="r1"> … depth: derivation, worked numbers, edge cases … </article>
</section>
```

**Rules.**

- **Same `data-concept` id in both panels.** The tabs are two depths of *one* concept, not two concepts. This is what lets the TOC, notes and detail panel keep working across a tab switch. See `components/reader-tools.md` §2 for the composite `panelId::conceptId` key that keeps per-tab *reading state* separate.
- **Tab ① must be self-sufficient on its own** (§2). Reading only the simple tab, opening nothing, switching nothing, the reader follows the whole argument. Tab ② adds depth; it never supplies a missing premise.
- **Disclosure nests.** Tab ② is *not* a licence to inline code. Inside the detail tab, code listings / templates / long tables still go to the right detail panel via block-level `data-expand` triggers (§4). The skimmable spine is per reading-mode (`SKILL.md` §2D).
- **Announce the split in the overview block** (§3): tell the reader what each tab holds and that ① alone is complete. Otherwise ② reads as withheld content.
- **Re-run panel-scoped scripts on switch.** Scroll-spy, notes concept-scan, progress counter and TOC all scope to `.tab-panel.active`. A `MutationObserver` on `.tab-panel[class]` is the cheapest hook.
- **Two tabs, never three.** A third depth is a second page.

Use this only when the depth tier is genuinely large (a thesis companion, a full course note). For a normal explainer, one column of briefs + the right detail panel (§4) is enough — the tab split doubles the authoring cost and the state-management surface.
