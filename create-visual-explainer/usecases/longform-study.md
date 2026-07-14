# Long-form Navigation Patterns

Navigation aids for long-form study notes, tutorials, and any page the reader will scroll through and skim. Read this when the page has ≥3 H2 sections and the reader needs to orient themselves, jump around, or feel progress.

These are *navigation primitives* for longform content. For the *aside / collapse / glossary* content patterns (accordion, sidenote, footnote, quiz, vocab tooltip), see `components/asides.md`.

**Quick map**: §1 TOC (sticky right rail) (L11–140) · §2 Reading-progress bar (L141–184) · §3 Section anchor (#-on-hover) (L185–214)

---

## 1. Table of Contents (TOC) — sticky right rail

For pages longer than ~5 sections. The TOC sits in a right-hand rail and highlights the current section using `IntersectionObserver`. For pages with 3-5 sections, prefer an inline list at top of the article instead — same `.toc-list` CSS, just placed inline rather than absolute-positioned.

### HTML

```html
<div class="layout-with-toc">
  <article class="prose-container">
    <h2 id="introduction">Introduction</h2>
    <p>...</p>
    <h2 id="cartoon">The cartoon picture</h2>
    <p>...</p>
    <h3 id="cartoon-balls">Rolling balls</h3>
    <h2 id="formal">Formal statement</h2>
  </article>

  <aside class="toc" aria-label="Table of contents">
    <span class="toc-label">ON THIS PAGE</span>
    <ol class="toc-list">
      <li><a href="#introduction" class="toc-link">Introduction</a></li>
      <li><a href="#cartoon"      class="toc-link">The cartoon picture</a>
        <ol>
          <li><a href="#cartoon-balls" class="toc-link toc-sub">Rolling balls</a></li>
        </ol>
      </li>
      <li><a href="#formal"       class="toc-link">Formal statement</a></li>
    </ol>
  </aside>
</div>
```

### CSS

```css
.layout-with-toc {
  display: grid;
  grid-template-columns: minmax(0, 1fr) 220px;
  gap: var(--sp-12);
  align-items: start;
  max-width: 1100px;
  margin-inline: auto;
  padding-inline: var(--sp-4);
}

.toc {
  position: sticky;
  top: var(--sp-8);
  max-height: calc(100vh - var(--sp-16));
  overflow-y: auto;
  padding: var(--sp-4) 0;
  border-left: 1px solid var(--border);
  padding-left: var(--sp-5);
}

.toc-label {
  display: block;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--text-dim);
  margin-bottom: var(--sp-3);
}

.toc-list {
  list-style: none;
  padding: 0; margin: 0;
}
.toc-list ol {
  list-style: none;
  padding-left: var(--sp-3);
  margin: var(--sp-1) 0;
}

.toc-link {
  display: block;
  font-size: var(--fs-sm);
  color: var(--text-muted);
  text-decoration: none;
  padding: var(--sp-1) 0;
  border-left: 2px solid transparent;
  padding-left: var(--sp-3);
  margin-left: calc(-1 * var(--sp-5));
  transition: color var(--dur-fast), border-color var(--dur-fast);
}
.toc-sub { font-size: var(--fs-xs); }

.toc-link:hover { color: var(--text-primary); }

.toc-link[data-active="true"] {
  color: var(--accent-green);
  border-left-color: var(--accent-green);
}

/* Hide TOC on smaller screens */
@media (max-width: 1024px) {
  .layout-with-toc { grid-template-columns: 1fr; }
  .toc { display: none; }
}
```

### JS — highlight current section while scrolling

```js
(function setupTOC() {
  const headings = document.querySelectorAll('article h2[id], article h3[id]');
  const links = document.querySelectorAll('.toc-link');
  const linkMap = new Map();
  links.forEach(a => linkMap.set(a.getAttribute('href').slice(1), a));

  const obs = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        links.forEach(a => a.removeAttribute('data-active'));
        const link = linkMap.get(entry.target.id);
        if (link) link.setAttribute('data-active', 'true');
      }
    });
  }, {
    rootMargin: '0px 0px -60% 0px',
    threshold: 0
  });

  headings.forEach(h => obs.observe(h));
})();
```

---

## 2. Reading-progress bar (top of viewport)

Linear bar at the very top of the page that fills as the reader scrolls. Subtle, ambient feedback that the article is finite.

```html
<div class="reading-progress" aria-hidden="true"><div class="reading-progress-fill"></div></div>
```

```css
.reading-progress {
  position: fixed;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: transparent;
  z-index: var(--z-toast);
  pointer-events: none;
}
.reading-progress-fill {
  height: 100%;
  width: 0%;
  background: var(--accent-green);
  transition: width 60ms linear;
}
```

```js
(function progressBar() {
  const fill = document.querySelector('.reading-progress-fill');
  if (!fill) return;
  function update() {
    const h = document.documentElement;
    const scrolled = (h.scrollTop) / (h.scrollHeight - h.clientHeight);
    fill.style.width = (Math.min(Math.max(scrolled, 0), 1) * 100) + '%';
  }
  window.addEventListener('scroll', update, { passive: true });
  window.addEventListener('resize', update);
  update();
})();
```

Note: this is *optional* per `SKILL.md` §2C — only include if user explicitly asks for it.

---

## 3. Section anchor (auto-link on hover)

Every section heading should be linkable. The `#` symbol appears on hover to the left of the heading.

```html
<h2 id="convergence">
  <a href="#convergence" class="anchor-link" aria-label="Link to this section">#</a>
  Convergence properties
</h2>
```

```css
h2 .anchor-link, h3 .anchor-link {
  position: absolute;
  margin-left: -1.2em;
  padding-right: 0.4em;
  color: var(--text-dim);
  font-family: var(--font-mono);
  text-decoration: none;
  opacity: 0;
  transition: opacity var(--dur-fast), color var(--dur-fast);
}
h2:hover .anchor-link, h3:hover .anchor-link { opacity: 1; }
.anchor-link:hover { color: var(--accent-green); }

h2[id], h3[id] { scroll-margin-top: var(--sp-12); }  /* prevents sticky nav from covering */
```

Note: this is *optional* per `SKILL.md` §2C — only include if user explicitly asks for it.
