# Reader Tools — notes, done-ticks, progress, focus mode

The **reader-tooling layer**: UI the reader operates *on top of* the content, not content itself. Notes panel, per-section done-ticks, reading-time estimates, completion counter, jump-to-next-unread, top reading-progress bar, and focus mode (dim everything except the section being read).

Its purpose is **executive-function support** — for ADHD/PDHD readers first, everyone else second. A dense explainer is a wall of undifferentiated text; these tools let the reader externalize memory, see progress, cut competing stimuli, and resume cheaply after an interruption. None of them change what the page teaches.

Depends on the `[data-concept]` contract (§2). Pairs with `usecases/progressive-disclosure.md` (same concept units, same panel-sharing discipline) and `usecases/longform-study.md` (TOC + anchors). Storage discipline in §8 is **mandatory** — `localStorage` throws in some sandboxes.

**Quick map**: §1 Why these tools — attention support (L13–34) · §2 The `data-concept` contract (L37–67) · §3 Top reading-progress bar (L70–108) · §4 Done-ticks + reading time (L111–195) · §5 Focus dock — counter + jump-to-next-unread (L198–264) · §6 Focus mode — dim, not blur (L267–330) · §7 Notes panel (L333–508) · §8 Persistence conventions (L511–537) · §9 A11y, print, reduced-motion, script order (L540–561) · §10 Anti-patterns (L564–578)

---

## 1. Why these tools — the attention-support argument

Each tool answers one concrete failure mode of reading a long technical page. Do not add them decoratively; each one buys a specific thing:

| Reader failure | Tool | What it buys |
|---|---|---|
| "I had a thought and lost it" | **Notes panel** (§7) | Externalized working memory. Thought leaves the head immediately, auto-attached to the section that caused it. |
| "How much is left? Is this endless?" | **Progress bar** (§3) + **counter** (§5) | Bounded task. An unbounded scroll is an anxiety generator; a `7/23 đã đọc` counter is a finite one. |
| "Everything on screen is shouting" | **Focus mode** (§6) | Reduced competing stimuli. One section at full contrast, the rest dimmed. |
| "I came back after 2 days — where was I?" | **Done-ticks** (§4) + **jump-to-next-unread** (§5) | Cheap re-entry. Zero cost to resume; the page remembers, the reader doesn't have to. |
| "Do I have time for this right now?" | **Reading-time** per section (§4) | Honest commitment estimate before starting, so a section is startable in a gap. |

**Three laws for this layer:**

1. **Reader-controlled, never automatic.** Focus mode is **off** by default and toggled by the reader. Ticks are set by the reader, never inferred from scroll position ("you scrolled past it" ≠ "you read it"). The page never decides the reader is done with something.
2. **Additive, never subtractive.** These tools must never hide, gate, or paywall content. Focus mode *dims*; it does not `display:none`. `Ctrl+F` must still find text in a dimmed section, and a screen reader must still read it. (Contrast: `usecases/progressive-disclosure.md` legitimately *hides* depth behind a click — that is a content decision, made at authoring time. This layer is a *view* decision, made at reading time.)
3. **State is local and disposable.** Notes and ticks live in `localStorage`, guarded (§8). Losing them must degrade the page to "a normal page", never to a broken one. Notes are exportable (§7.5) precisely because `localStorage` is not a safe place for anything the reader cares about.

Cognitive-load tie-in: `foundations/pedagogy.md` §5 caps *new terms per section*; this layer caps *simultaneous demands on attention*. Same budget, different axis.

**Default inclusion.** For any page with ≥5 concepts: progress bar + done-ticks + counter + focus mode ship by default (they are attention infrastructure, not extras). The notes panel ships by default on study/reference pages the reader will return to; skip it on a one-sitting explainer. See `SKILL.md` §2E.

---

## 2. The `data-concept` contract

Everything in this file, plus the detail panel in `usecases/progressive-disclosure.md`, joins on one attribute. Define it once, at authoring time:

```html
<article class="concept" data-concept="r1">
  <h3><span class="cid">R1</span> Giải phẫu Research Question</h3>
  ...
</article>

<!-- the overview / advance organizer is a concept too -->
<section class="overview" data-concept="r0" id="ov"> … </section>
```

**Rules:**

- Every markable unit carries a **unique, stable** `data-concept` id (`r0`, `r1`, …). Stable = it survives an edit of the heading text, because ticks and notes are keyed by it.
- The first `h2`/`h3` inside is the unit's heading; tooling appends its chrome (`.rp-tag`) there.
- A leading `<span class="cid">R1</span>` gives a short display label for notes tags and the "đang đọc" indicator. Optional but recommended.
- Sections are collected **in document order** — every scroll-spy here relies on that (`for … if (top <= ref) chosen = el; else break;`).

**Composite keys when the page has multiple reading modes.** If the page has a simple/detail tab split (`usecases/progressive-disclosure.md` §7), the *same* `data-concept="r1"` exists in both panels. Keying `done` by the bare id makes ticking R1 in the simple tab silently tick it in the detail tab. Key by `panelId::conceptId`:

```js
function panelOf(el){ var p = el.closest(".tab-panel"); return p ? p.id : "loose"; }
function cidOf(el){ return panelOf(el) + "::" + el.getAttribute("data-concept"); }
// → "tab-simple::r1"  vs  "tab-detail::r1"
```

Notes, by contrast, are keyed by the **bare** `conceptId` — a thought about R1 is a thought about R1 regardless of which depth you had open.

---

## 3. Top reading-progress bar

Scroll position, 3px, identity accent. This is the *cheapest* progress signal and the least honest one — it measures pixels, not comprehension. It is worth shipping anyway because it bounds the task visually; pair it with the real counter in §5.

`usecases/longform-study.md` §2 has the base pattern. The reader-tools variant adds a glow and is injected by JS (no markup to maintain):

```css
#reading-bar{ position:fixed; top:0; left:0; right:0; height:3px; z-index:50;
  pointer-events:none; background:transparent; }
#reading-bar > i{ display:block; height:100%; width:0%;
  background:var(--accent-identity);
  box-shadow:0 0 8px rgb(var(--accent-identity-rgb)/.6);
  transition:width .1s linear; }
```

```js
var bar = document.createElement("div"); bar.id = "reading-bar";
var fill = document.createElement("i"); bar.appendChild(fill);
document.body.appendChild(bar);

var ticking = false;
function onScroll(){
  if (ticking) return; ticking = true;
  requestAnimationFrame(function(){
    var se = document.documentElement;
    var max = se.scrollHeight - se.clientHeight;
    var pct = max > 0 ? se.scrollTop / max : 0;
    fill.style.width = (Math.min(1, Math.max(0, pct)) * 100).toFixed(1) + "%";
    if (focusOn) computeActive();          // §6
    ticking = false;
  });
}
window.addEventListener("scroll", onScroll, { passive:true });
window.addEventListener("resize", onScroll);
onScroll();
```

`transition:width` is the one sanctioned exception to visual rule 9 (animate `transform`/`opacity` only): the bar is 3px tall, the layout can't reflow, and a `scaleX` transform would smear the glow. Keep it under `.1s linear`.

---

## 4. Done-ticks + reading time

A circular tick and a `~4 phút` estimate appended to each concept heading. Ticked sections drop to `opacity:.6` and come back on hover — visibly *done*, never gone.

### CSS

```css
.rp-tag{ display:inline-flex; align-items:center; gap:8px; margin-left:10px;
  vertical-align:middle; font-weight:400; }
.rp-time{ font-family:var(--font-mono); font-size:var(--fs-nano); letter-spacing:.04em;
  color:var(--text-dim); text-transform:none; }

.rp-check{ width:20px; height:20px; border-radius:50%; border:1.5px solid var(--border);
  background:transparent; color:transparent; font-size:11px; line-height:1; cursor:pointer;
  display:inline-flex; align-items:center; justify-content:center; flex:0 0 auto; padding:0;
  transition:all var(--dur-fast); }
.rp-check:hover{ border-color:var(--accent-identity); color:rgb(var(--accent-identity-rgb)/.5); }

.concept.is-done > h3 .rp-check,
.overview.is-done > h2 .rp-check,
.is-done .rp-check{ background:var(--accent-identity); border-color:var(--accent-identity); color:var(--bg); }

.concept.is-done, .overview.is-done{ opacity:.6; transition:opacity var(--dur-base) var(--ease-out); }
.concept.is-done:hover, .overview.is-done:hover{ opacity:1; }
```

`.rp-time` uses `--text-dim`, which is decorative-only per visual rule 7 — legal here because it is a redundant hint, never the only carrier of information.

### JS — inject, persist, toggle

```js
var K_DONE = "<page-slug>-done-v1";
var WPM = 160;                                   // technical Vietnamese, rough
var done = {};
function saveDone(){ try { localStorage.setItem(K_DONE, JSON.stringify(Object.keys(done))); } catch(e){} }
try { var raw = localStorage.getItem(K_DONE); if (raw) JSON.parse(raw).forEach(function(id){ done[id]=1; }); } catch(e){}

// reading time — strip math, code and injected chrome before counting words
function wordsIn(el){
  var c = el.cloneNode(true);
  c.querySelectorAll(".katex, .katex-display, pre, code, .rp-tag, script, style")
   .forEach(function(n){ n.remove(); });
  var t = (c.textContent || "").trim();
  return t ? t.split(/\s+/).length : 0;
}

var article  = document.querySelector(".article");
var concepts = Array.prototype.slice.call(article.querySelectorAll("[data-concept]"));

concepts.forEach(function(el){
  var head = el.querySelector("h2, h3");
  if (!head || head.querySelector(".rp-tag")) return;   // idempotent
  el.__cid = cidOf(el);                                  // §2 — composite if tabs exist
  el.__markable = true;
  var mins = Math.max(1, Math.round(wordsIn(el) / WPM));

  var tag = document.createElement("span"); tag.className = "rp-tag";
  var chk = document.createElement("button");
  chk.type = "button"; chk.className = "rp-check"; chk.textContent = "\u2713";
  chk.dataset.cid = el.__cid;
  chk.setAttribute("aria-label", "Đánh dấu đã đọc phần này");
  var tm = document.createElement("span"); tm.className = "rp-time";
  tm.textContent = "~" + mins + " phút";
  tag.appendChild(chk); tag.appendChild(tm);
  head.appendChild(tag);
});

function applyDone(){
  concepts.forEach(function(el){ if (el.__markable) el.classList.toggle("is-done", !!done[el.__cid]); });
}
function toggleDone(cid){
  if (done[cid]) delete done[cid]; else done[cid] = 1;
  saveDone(); applyDone(); updateProgress();            // §5
}
article.addEventListener("click", function(e){          // delegated — survives re-render
  var b = e.target.closest(".rp-check"); if (!b) return;
  e.preventDefault(); e.stopPropagation(); toggleDone(b.dataset.cid);
});
applyDone();
```

**Reading time honesty.** `WPM = 160` is for dense technical prose in Vietnamese; use ~200 for English, ~120 if the section is proof-heavy. Excluding KaTeX and `<pre>` from the word count is not an optimization — including them reports "2 phút" for a page of algebra that takes twenty. Round up (`Math.max(1, …)`), never report `0 phút`.

Ideally the tick would be `role="checkbox"` + `aria-checked`. A `<button>` with a state-carrying `aria-label` (`"Đã đọc — bấm để bỏ đánh dấu"`) is the simpler correct alternative; pick one and keep the state in the accessible name.

---

## 5. Focus dock — completion counter + jump-to-next-unread

Bottom-right, two pills: real completion (`⬔ 7/23 đã đọc`, clicking scrolls to the first unread concept) and the focus toggle (§6).

```css
#focus-dock{ position:fixed; right:16px; bottom:16px; z-index:90;
  display:flex; flex-direction:column; gap:8px; align-items:flex-end; }

.fd-btn{ display:inline-flex; align-items:center; gap:8px; font-family:var(--font-mono);
  font-size:var(--fs-xs); letter-spacing:.03em; color:var(--text-secondary);
  background:var(--card); border:1px solid var(--border); border-radius:var(--r-pill);
  padding:8px 14px; cursor:pointer; box-shadow:0 6px 20px rgba(0,0,0,.28);
  transition:color var(--dur-fast), border-color var(--dur-fast),
             background var(--dur-fast), filter var(--dur-fast); }
.fd-btn:hover{ color:var(--text-primary); border-color:var(--text-muted); }
.fd-btn .fd-ic{ font-size:14px; line-height:1; }

#fp-progress .fd-frac{ color:var(--accent-identity); font-weight:700; }
#fp-focus[aria-pressed="true"]{ color:var(--bg); background:var(--accent-identity);
  border-color:var(--accent-identity); }
#fp-focus[aria-pressed="true"]:hover{ filter:brightness(1.08); color:var(--bg); }
```

The dock is one of the few places `box-shadow` is legal (visual rule 2): it is a true floating overlay, not chrome.

```js
var dock = document.createElement("div"); dock.id = "focus-dock";
var prog = document.createElement("button");
prog.type="button"; prog.className="fd-btn"; prog.id="fp-progress";
prog.title = "Nhảy tới phần chưa đọc tiếp theo";
prog.innerHTML = '<span class="fd-ic">\u25F1</span><span><span class="fd-frac">0</span>/<span class="fd-total">0</span> đã đọc</span>';
var foc = document.createElement("button");
foc.type="button"; foc.className="fd-btn"; foc.id="fp-focus";
foc.setAttribute("aria-pressed","false");
foc.innerHTML = '<span class="fd-ic">\u25D0</span><span>Focus</span>';
dock.appendChild(prog); dock.appendChild(foc); document.body.appendChild(dock);

var fracEl = prog.querySelector(".fd-frac"), totEl = prog.querySelector(".fd-total");

// count only the reading mode currently open (see §2 composite keys)
function currentPanelId(){ var p = document.querySelector(".tab-panel.active"); return p ? p.id : ""; }
function updateProgress(){
  var pid = currentPanelId(), d = 0, tot = 0;
  concepts.forEach(function(el){
    if (!el.__markable) return;
    var p = el.closest(".tab-panel");
    if (p && p.id !== pid) return;
    tot++; if (done[el.__cid]) d++;
  });
  fracEl.textContent = d; totEl.textContent = tot;
}
updateProgress();

function scrollToEl(el){
  var y = window.pageYOffset + el.getBoundingClientRect().top - (refOffset() - 60);
  window.scrollTo({ top: Math.max(0, y), behavior:"smooth" });
}
prog.addEventListener("click", function(){
  var vis = activeConcepts();                                   // §6
  for (var i=0;i<vis.length;i++)
    if (vis[i].__markable && !done[vis[i].__cid]) { scrollToEl(vis[i]); return; }
  window.scrollTo({ top:0, behavior:"smooth" });                // all done → back to top
});
```

**Jump-to-next-unread is the highest-value button on the page** for an interrupted reader: it converts "find my place" (a search task, expensive) into "press one thing" (a motor task, free). Never make the reader hunt.

---

## 6. Focus mode — dim, not blur

Everything except the section under the reading line drops to `opacity:.1` + `saturate(.6)`. Hovering a dimmed section raises it to `.9` so the reader can peek without leaving the mode.

```css
body.focus-on .concept, body.focus-on .overview{
  opacity:.1; filter:saturate(.6);
  transition:opacity .25s var(--ease-out), filter .25s var(--ease-out); }
body.focus-on .concept.focus-active, body.focus-on .overview.focus-active{ opacity:1; filter:none; }
body.focus-on .concept:hover, body.focus-on .overview:hover{ opacity:.9; }
body.focus-on .concept.focus-active:hover, body.focus-on .overview.focus-active:hover{ opacity:1; }
```

**Why `opacity`, not `filter: blur()`.** Blurring body text forces the eye to keep attempting accommodation on something it can never resolve — for many readers that is actively unpleasant, and for low-vision readers it destroys the page. Opacity fades the text *out of the figure/ground competition* without asking the eye to focus on it. Blur is also expensive to composite over a full-page column. Use blur only on a *backdrop* behind a modal (`components/core.md` §7), never on prose. Desaturating alongside the fade kills residual color pop from accents and syntax highlighting, which is what actually pulls the eye.

Do not go to `opacity:0` — invisible is a different UX from de-emphasized, and it makes the page look broken while scrolling.

```js
var K_FOCUS = "<page-slug>-focus-v1";
var focusOn = false;
try { focusOn = localStorage.getItem(K_FOCUS) === "1"; } catch(e){}

function activePanel(){ return document.querySelector(".tab-panel.active") || document.querySelector(".tab-panel"); }
function activeConcepts(){
  var p = activePanel();
  return p ? Array.prototype.slice.call(p.querySelectorAll("[data-concept]")) : concepts;
}
// the "reading line": ~140px under the sticky header
function refOffset(){
  var ab = document.querySelector(".appbar");
  return (ab ? ab.getBoundingClientRect().height : 60) + 140;
}

var activeEl = null;
function setActive(el){
  if (el === activeEl) return;
  if (activeEl) activeEl.classList.remove("focus-active");
  activeEl = el;
  if (activeEl && focusOn) activeEl.classList.add("focus-active");
}
function computeActive(){                      // document order → last one above the line
  var list = activeConcepts(); if (!list.length){ setActive(null); return; }
  var ref = refOffset(), chosen = null;
  for (var i=0;i<list.length;i++){
    if (list[i].getBoundingClientRect().top <= ref) chosen = list[i]; else break;
  }
  setActive(chosen || list[0]);
}
function setFocus(on){
  focusOn = on;
  document.body.classList.toggle("focus-on", on);
  foc.setAttribute("aria-pressed", on ? "true" : "false");
  try { localStorage.setItem(K_FOCUS, on ? "1" : "0"); } catch(e){}
  concepts.forEach(function(c){ c.classList.remove("focus-active"); });
  if (on){ activeEl = null; computeActive(); }
}
foc.addEventListener("click", function(){ setFocus(!focusOn); });
setFocus(focusOn);                              // restore last session
```

**Reading line, not viewport center.** Choosing "the last concept whose top is above `header + 140px`" means the active section is the one the reader is *reading into*, not the one occupying the most pixels. A viewport-center rule flickers between two sections at every boundary.

If the page has tabs, re-run on switch: a `MutationObserver` on `.tab-panel[class]` that clears `.focus-active`, calls `updateProgress()` and recomputes.

---

## 7. Notes panel — local, context-aware, no AI

A left-edge bookmark launcher opens a slide-in panel. Every note auto-attaches to the concept the reader is currently on. Filter `Phần này / Tất cả`, edit, delete-with-confirm, export `.md` / `.json`, import back.

Left edge on purpose: the right rail already belongs to the TOC (`usecases/longform-study.md` §1) and the detail panel (`usecases/progressive-disclosure.md` §4). Notes and depth must never fight for the same rail.

### 7.1 Markup

```html
<button class="notes-launcher" id="notes-launcher" type="button"
        aria-label="Mở bảng ghi chú" aria-expanded="false" aria-controls="notes-panel">
  <span class="nl-icon" aria-hidden="true">✎</span>
  <span class="nl-text">Ghi chú</span>
  <span class="nl-count" id="nl-count" hidden>0</span>
</button>
<div class="notes-backdrop" id="notes-backdrop"></div>

<aside class="notes-panel" id="notes-panel" aria-label="Bảng ghi chú" aria-hidden="true">
  <header class="notes-head">
    <span class="notes-title">Ghi chú</span>
    <span class="notes-total" id="notes-total">0</span>
    <button class="notes-close" id="notes-close" type="button" aria-label="Đóng bảng ghi chú">✕</button>
  </header>

  <div class="notes-context">
    <span class="nc-label">Đang đọc</span>
    <button class="nc-where" id="nc-where" type="button" title="Cuộn tới phần đang đọc">— cuộn để bắt đầu —</button>
  </div>

  <div class="notes-compose">
    <textarea class="notes-input" id="notes-input" rows="3"
      placeholder="Viết ghi chú cho phần đang đọc… — Enter để lưu, Shift+Enter xuống dòng"></textarea>
    <div class="notes-compose-row">
      <span class="notes-attach" id="notes-attach">Gắn vào: <b>—</b></span>
      <button class="notes-add" id="notes-add" type="button" disabled>Thêm ghi chú</button>
    </div>
  </div>

  <div class="notes-filter">
    <button class="nf-btn active" id="nf-here" type="button" data-filter="here">Phần này</button>
    <button class="nf-btn" id="nf-all" type="button" data-filter="all">Tất cả</button>
    <span class="nf-spacer"></span>
    <button class="notes-io"     id="notes-import"      type="button">Nhập</button>
    <button class="notes-export" id="notes-export-json" type="button">Xuất .json</button>
    <button class="notes-export" id="notes-export"      type="button">Xuất .md</button>
    <input id="notes-import-file" type="file" accept=".json,.md,.txt" hidden>
  </div>

  <div class="notes-io-msg" id="notes-io-msg" hidden></div>
  <div class="notes-list" id="notes-list" aria-live="polite"></div>
</aside>
```

### 7.2 CSS (essentials)

```css
:root{ --notes-w:344px; }

.notes-launcher{
  position:fixed; left:0; top:50%; transform:translateY(-50%);
  z-index:calc(var(--z-toast) - 2);
  display:flex; flex-direction:column; align-items:center; gap:var(--sp-2);
  padding:var(--sp-4) var(--sp-2); background:var(--card); color:var(--text-secondary);
  border:1px solid var(--border); border-left:none; border-radius:0 var(--r-md) var(--r-md) 0;
  cursor:pointer; box-shadow:6px 0 22px rgba(0,0,0,.26);
  transition:color var(--dur-fast), background var(--dur-fast), transform var(--dur-base), opacity var(--dur-base); }
.notes-launcher:hover{ color:var(--accent-identity); background:var(--surface); }
.nl-text{ writing-mode:vertical-rl; text-orientation:mixed; font-family:var(--font-mono);
  font-size:var(--fs-micro); letter-spacing:.16em; text-transform:uppercase; }
.nl-count{ min-width:17px; height:17px; padding:0 5px; border-radius:var(--r-pill);
  font-family:var(--font-mono); font-size:var(--fs-nano); font-variant-numeric:tabular-nums;
  color:var(--bg); background:var(--accent-identity); display:grid; place-items:center; }
body.notes-open .notes-launcher{ opacity:0; pointer-events:none; transform:translate(-110%,-50%); }

.notes-panel{ position:fixed; top:0; left:0; height:100dvh; width:var(--notes-w);
  display:flex; flex-direction:column; background:var(--surface);
  border-right:1px solid var(--border); z-index:calc(var(--z-toast) - 1);
  transform:translateX(-100%); transition:transform var(--dur-base) var(--ease-out); }
.notes-panel.open{ transform:translateX(0); box-shadow:26px 0 52px rgba(0,0,0,.34); }

.notes-list{ flex:1 1 auto; overflow-y:auto; padding:var(--sp-4) var(--sp-5) var(--sp-8); }
.notes-item{ background:var(--card); border:1px solid var(--border);
  border-left:3px solid rgb(var(--accent-identity-rgb)/.55);
  border-radius:0 var(--r-md) var(--r-md) 0;
  padding:var(--sp-3) var(--sp-4); margin-bottom:var(--sp-3); }
.ni-text{ font-size:var(--fs-sm); line-height:1.6; color:var(--text-primary);
  white-space:pre-wrap; word-break:break-word; }
.ni-meta{ display:flex; align-items:center; gap:var(--sp-2); margin-top:var(--sp-3); flex-wrap:wrap; }
.ni-tag{ font-family:var(--font-mono); font-size:var(--fs-nano); letter-spacing:.05em; text-transform:uppercase;
  color:var(--accent-identity); background:rgb(var(--accent-identity-rgb)/.1);
  border:1px solid rgb(var(--accent-identity-rgb)/.22); border-radius:var(--r-pill);
  padding:2px 8px; cursor:pointer; }              /* triple-opacity, rule 1 */
.ni-time{ font-family:var(--font-mono); font-size:var(--fs-nano); color:var(--text-dim); }
.ni-actions{ margin-left:auto; display:flex; gap:var(--sp-1); }
.ni-btn.del:hover, .ni-btn.del.armed{ color:var(--accent-red); }

.notes-panel button:focus-visible, .notes-launcher:focus-visible{
  outline:2px solid var(--accent-identity); outline-offset:2px; }

/* wide: push the article aside. narrow: overlay + backdrop. */
@media (min-width:1024px){
  body{ transition:padding-left var(--dur-base) var(--ease-out); }
  body.notes-open{ padding-left:var(--notes-w); }
  body.notes-open .notes-backdrop{ display:none; }
}
@media (max-width:1023px){ .notes-panel{ width:min(88vw,360px); } }
@media (prefers-reduced-motion:reduce){ .notes-panel, .notes-launcher, body{ transition:none; } }
@media print{
  .notes-launcher, .notes-panel, .notes-backdrop{ display:none !important; }
  body{ padding-left:0 !important; }
}
```

Push-not-overlay above 1024px is deliberate: taking a note *while reading the thing you're taking a note about* is the whole point. An overlay that covers the text forces the reader to hold the thought in the head — exactly what the panel exists to prevent.

### 7.3 Note schema + the "đang đọc" tracker

```js
// note: { id, text, conceptId, cid, label, ts }
//   conceptId — BARE data-concept (not panel-scoped; §2)
//   cid       — short display label, e.g. "R1"
//   label     — "Phần I · R1 — Giải phẫu Research Question" (for grouping + export headings)
//   ts        — Date.now(), for "3 giờ trước" and stable sort inside a concept
```

Scan the active panel in document order and reuse the §6 reading-line rule to decide which concept a new note attaches to. On change, update the `Đang đọc` button, the `Gắn vào: <b>R1</b>` hint, and re-render the list if the filter is `here`:

```js
function conceptInfo(el){
  var h = el.querySelector("h3, h2");
  if (!h) return { cid:"", title:"Tổng quan" };
  var clone = h.cloneNode(true);
  var cidEl = clone.querySelector(".cid");
  var cid = cidEl ? cidEl.textContent.trim() : "";
  clone.querySelectorAll("span").forEach(function(s){ s.remove(); });  // drop cid + .rp-tag chrome
  return { cid:cid, title:(clone.textContent||"").replace(/\s+/g," ").trim() || "Mục" };
}
function scanConcepts(){                       // re-run on tab switch
  trackedEls = [];
  var panelEl = activePanelEl(); if (!panelEl) return;
  panelEl.querySelectorAll("[data-concept]").forEach(function(el){
    var info = conceptInfo(el), part = partOf(el);
    trackedEls.push({ el:el, conceptId:el.getAttribute("data-concept"),
      cid:info.cid, title:info.title, part:part,
      label:(part ? part + " · " : "") + (info.cid ? info.cid + " — " : "") + info.title });
  });
}
```

A note taken before the reader has scrolled to any concept gets `conceptId:"_general"` and groups under `Chung / không gắn phần`. Never drop it, never refuse to save it.

### 7.4 Compose, edit, delete

- `Enter` saves, `Shift+Enter` newlines. The add button stays `disabled` while the textarea is empty (no silent no-op).
- Edit swaps the item body for a `<textarea>`; `Enter` commits, `Esc` cancels.
- Delete is **two-click armed**, not a modal: first click turns `Xoá` into `Xoá?` for 2.5s, second click deletes. A confirm dialog for a one-line note is a tax; an accidental permanent delete is worse. Arming resolves both.
- Render with `textContent`, never `innerHTML` — a note is untrusted text and will contain `<`.

### 7.5 Export + import

Notes in `localStorage` are one cleared-cache away from gone. Two exports, one importer:

- **`.md`** — human-facing. Sorted by *reading order* (not by timestamp), grouped under `## <label>` headings, one `- ` bullet per note, continuation lines indented 2 spaces. This is what gets pasted into a thesis draft.
- **`.json`** — lossless round-trip. `{id, text, conceptId, cid, label, ts}`.

```js
function readingIndex(conceptId){                        // sort by page order, not clock
  for (var i=0;i<trackedEls.length;i++) if (trackedEls[i].conceptId === conceptId) return i;
  return conceptId === "_general" ? 9998 : 9999;         // orphans last
}
```

**Import must merge, never replace.** Re-attach each incoming note by `conceptId` (falling back to `cid`, then `label`) against the current page's concept index, since a note may come from an older revision. De-duplicate on `conceptId + "\0" + text.trim()` and report the result honestly: `Đã thêm 12 · bỏ qua 3 trùng`. Accept both `.json` and the exported `.md` (parse `## heading` → group, `- ` → note, indented lines → continuation) — the reader should never be punished for re-importing the file they were given to read.

Show outcome in `#notes-io-msg`; on a parse failure say what failed, don't silently swallow. The `nl-count` badge on the launcher shows the total and stays `hidden` at zero.

---

## 8. Persistence conventions

```js
var KEY     = "<page-slug>-notes-v1";
var K_DONE  = "<page-slug>-done-v2";
var K_FOCUS = "<page-slug>-focus-v1";
```

1. **Always guard.** Every read and write goes in `try { … } catch(e){}`. `localStorage` throws in private-mode Safari, under strict cookie blocking, in cross-origin iframes, and **in the claude.ai artifact sandbox, where it is unavailable by design**. A guarded call degrades to an in-memory session; an unguarded call kills every script below it on the page. This is the single most common way a page like this dies silently.
2. **Namespace + version every key** (`<page-slug>-<feature>-v<N>`). Two explainers on the same origin must not collide.
3. **Migrate, never wipe.** Bumping a schema version means reading the old key once and translating. Real example — moving done-ticks from bare `conceptId` to `panelId::conceptId` (§2):

```js
var raw = localStorage.getItem(K_DONE);
if (raw != null) { JSON.parse(raw).forEach(function(id){ done[id]=1; }); }
else {
  var old = localStorage.getItem(K_DONE_OLD);
  if (old) {                                   // v1 ticks were shared across tabs
    JSON.parse(old).forEach(function(id){ done["tab-simple::"+id]=1; done["tab-detail::"+id]=1; });
    saveDone();                                // upgrade in place; nobody loses progress
  }
}
```

4. **Store the minimum.** Ticks are an array of ids, not an object of objects. Focus is `"1"`/`"0"`.
5. **If the deliverable is a claude.ai artifact**, say so in the handoff: notes persist for the session only. Point the reader at `Xuất .md` before they close the tab, and at downloading the standalone `.html` if they want persistence. The verification trail (rule 16) is the right place for this.

---

## 9. A11y, print, reduced-motion, script order

```css
@media print{
  #reading-bar, #focus-dock{ display:none !important; }
  .rp-tag{ display:none !important; }
  .concept, .overview{ opacity:1 !important; filter:none !important; }   /* un-dim everything */
}
@media (prefers-reduced-motion:reduce){
  #reading-bar > i, body.focus-on .concept, body.focus-on .overview,
  .concept.is-done, .overview.is-done{ transition:none; }
}
```

- Print restores every dimmed and ticked section to full opacity. A printout of a focus-mode page must not be a page of ghosts.
- Focus mode's *end state* survives reduced-motion; only the transition is dropped. Reduced motion means no animation, not no feature.
- `:focus-visible` outlines on every injected button (they are keyboard destinations that did not exist in the source HTML).
- The focus toggle carries `aria-pressed`; the launcher carries `aria-expanded` + `aria-controls`; the notes list is `aria-live="polite"` so an added note is announced.
- Panel opens: move focus to the textarea, trap `Esc` to close, restore focus to the launcher. When the panel is closed set `aria-hidden="true"` on it (and never on an element containing the focused node).
- **Script order matters.** Run this layer on `window.load`, *after* KaTeX has rendered and after any tab controller exists. Reading-time counts words in the final DOM; if KaTeX hasn't run, `$\sigma^2$` counts as words and the estimate is wrong. Keep it in its own IIFE with its own `"use strict"` so a throw here can't take down the tab controller.
- Injecting chrome must be **idempotent** (`if (head.querySelector(".rp-tag")) return;`) — tab switches and re-renders will call it twice.

---

## 10. Anti-patterns

- ❌ **`filter: blur()` on body text to de-emphasize it** → forces futile eye accommodation and wrecks low-vision readability. Dim with `opacity` + `saturate()` (§6). Blur belongs on modal backdrops only.
- ❌ **Auto-ticking a section because the reader scrolled past it** → scrolling is not reading; a false "done" destroys the counter's only value, which is that the reader trusts it.
- ❌ **Focus mode on by default** → the reader has not asked to have the page taken away from them. Off, persisted, reader-toggled.
- ❌ **`opacity:0` or `display:none` for non-active sections** → breaks `Ctrl+F`, screen readers, and looks like a rendering bug.
- ❌ **Unguarded `localStorage`** → throws in the artifact sandbox and private mode, taking every script after it down with it (§8).
- ❌ **A notes panel with no export** → the reader's thinking is trapped in a browser profile. `.md` + `.json`, always.
- ❌ **Import that replaces instead of merges** → one mis-click destroys weeks of notes.
- ❌ **Note rendered with `innerHTML`** → self-XSS on your own reader's text.
- ❌ **Notes panel on the right rail** → collides with the TOC and the detail panel (`usecases/progressive-disclosure.md` §4). Left edge.
- ❌ **A progress bar as the *only* progress signal** → it measures scroll, not comprehension. Pair it with the `x/y đã đọc` counter.
- ❌ **Bare `conceptId` as the done-key on a two-tab page** → ticking a concept in the simple tab silently ticks it in the detail tab (§2).
- ❌ **Reading time computed over KaTeX and `<pre>`** → a page of derivations reports "2 phút" and the reader learns to distrust every number on the page.
- ❌ **Chrome injected without an idempotence guard** → two ticks and two `~4 phút` labels after a tab switch.
