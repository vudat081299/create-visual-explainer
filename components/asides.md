# Aside / Collapse / Glossary Patterns

Patterns for content that lives *beside* the main prose: collapsible reveals, margin notes, footnotes, self-check quizzes, and inline glossary tooltips. Read this when you're writing a study note or tutorial and need to add hide-show solutions, side commentary, citations, or term definitions.

These are *content primitives* for layered learning. For *navigation aids* on the same long pages (TOC, reading progress, section anchors), see `usecases/longform-study.md`. For theorem/proof boxes and equation containers, see `usecases/math-learning.md`.

**Quick map**: §1 Accordion / Collapsible (show solution) (L11–127) · §2 Sidenote (Tufte margin note) (L128–209) · §3 Footnotes (L210–273) · §4 Self-check / Quiz (L274–383) · §5 Vocab tooltip (L384–435) · §6 Pedagogy tie-in (L436–450)

---

## 1. Accordion / Collapsible — "show solution"

The single most useful component for math/DS learning. "Try it yourself, then click to reveal the answer."

### HTML — solo (no group)

```html
<details class="collapsible">
  <summary>
    <span class="collapsible-label">SOLUTION</span>
    <span class="collapsible-hint">Click to reveal</span>
    <span class="collapsible-icon" aria-hidden="true">+</span>
  </summary>
  <div class="collapsible-body">
    <p>The derivative of $x^3$ is $3x^2$. Evaluating at $x=2$: $3 \cdot 4 = 12$.</p>
  </div>
</details>
```

### CSS

```css
.collapsible {
  border: 1px solid var(--border);
  border-radius: var(--r-md);
  background: var(--card);
  margin: var(--sp-5) 0;
  overflow: hidden;
}

.collapsible > summary {
  display: flex; align-items: center; gap: var(--sp-3);
  padding: var(--sp-3) var(--sp-5);
  cursor: pointer;
  list-style: none;                 /* hide default disclosure triangle */
  user-select: none;
  background: var(--surface);
  transition: background var(--dur-fast);
}
.collapsible > summary::-webkit-details-marker { display: none; } /* Safari */
.collapsible > summary:hover { background: var(--hairline); }

.collapsible-label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--accent-yellow);
}
.collapsible-hint {
  font-family: var(--font-body);
  font-size: var(--fs-xs);
  color: var(--text-muted);
}
.collapsible-icon {
  margin-left: auto;
  font-family: var(--font-mono);
  font-size: var(--fs-lg);
  color: var(--text-muted);
  transition: transform var(--dur-base) var(--ease-out);
}
.collapsible[open] > summary > .collapsible-icon { transform: rotate(45deg); }
.collapsible[open] > summary > .collapsible-hint { display: none; }

.collapsible-body {
  padding: var(--sp-5);
  border-top: 1px solid var(--border);
  /* slide-down animation */
  animation: collapsible-open var(--dur-base) var(--ease-out);
}
@keyframes collapsible-open {
  from { opacity: 0; transform: translateY(-4px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Variants by purpose */
.collapsible[data-variant="solution"] .collapsible-label { color: var(--accent-yellow); }
.collapsible[data-variant="hint"]     .collapsible-label { color: var(--accent-cyan); }
.collapsible[data-variant="proof"]    .collapsible-label { color: var(--accent-blue); }
.collapsible[data-variant="aside"]    .collapsible-label { color: var(--accent-purple); }
```

### Accordion group — exclusive open

For FAQ-style sections where opening one item should close others.

```html
<div class="accordion" data-exclusive>
  <details class="collapsible" name="faq">
    <summary><span class="collapsible-label">Q1</span> Why does gradient descent overshoot?</summary>
    <div class="collapsible-body">...</div>
  </details>
  <details class="collapsible" name="faq">
    <summary><span class="collapsible-label">Q2</span> What does momentum buy us?</summary>
    <div class="collapsible-body">...</div>
  </details>
</div>
```

The `name` attribute on `<details>` (Chrome 120+, Safari 17.4+) makes the group exclusive natively. For older browsers, fall back to JS:

```js
document.querySelectorAll('.accordion[data-exclusive]').forEach(group => {
  group.querySelectorAll('details').forEach(d => {
    d.addEventListener('toggle', () => {
      if (d.open) {
        group.querySelectorAll('details').forEach(other => {
          if (other !== d) other.open = false;
        });
      }
    });
  });
});
```

---

## 2. Sidenote / Margin note (Tufte-style)

Inline margin annotations. Better than footnotes for short asides because the reader doesn't lose context.

```html
<p>Gradient descent is sensitive to the choice of learning rate.<span class="sidenote-ref">1</span></p>
<aside class="sidenote">
  <span class="sidenote-num">1</span>
  Specifically, $\alpha < 2/L$ where $L$ is the Lipschitz constant of $\nabla f$.
</aside>
```

```css
/* Default placement: float right of the paragraph */
@media (min-width: 1100px) {
  .prose-container { position: relative; }
  .sidenote {
    position: absolute;
    right: -240px;
    width: 220px;
    font-family: var(--font-body);
    font-size: var(--fs-xs);
    line-height: 1.5;
    color: var(--text-muted);
    border-left: 2px solid var(--accent-cyan);
    padding-left: var(--sp-3);
    /* Aligned with the line that referenced it; JS below handles alignment */
  }
  .sidenote-num {
    color: var(--accent-cyan);
    font-family: var(--font-mono);
    font-size: var(--fs-micro);
    letter-spacing: 0.1em;
    margin-right: var(--sp-1);
    vertical-align: super;
  }
  .sidenote-ref {
    color: var(--accent-cyan);
    font-family: var(--font-mono);
    font-size: 0.7em;
    vertical-align: super;
    cursor: pointer;
  }
}

/* Below 1100px: collapse to inline note (block beneath paragraph) */
@media (max-width: 1099px) {
  .sidenote {
    display: block;
    margin: var(--sp-3) 0;
    padding: var(--sp-2) var(--sp-4);
    background: rgb(var(--accent-cyan-rgb) / 0.05);
    border-left: 2px solid var(--accent-cyan);
    border-radius: 0 var(--r-sm) var(--r-sm) 0;
    font-size: var(--fs-sm);
    color: var(--text-secondary);
  }
}
```

### Aligning sidenote to paragraph (JS)

```js
function alignSidenotes() {
  if (window.innerWidth < 1100) return;
  document.querySelectorAll('.sidenote-ref').forEach(ref => {
    const num = ref.textContent.trim();
    const note = document.querySelector(`.sidenote .sidenote-num`)?.parentElement;
    // Match by num; pseudo-code — wire as needed
    const refRect = ref.getBoundingClientRect();
    const parentRect = ref.closest('.prose-container').getBoundingClientRect();
    if (note) note.style.top = (refRect.top - parentRect.top) + 'px';
  });
}
window.addEventListener('load', alignSidenotes);
window.addEventListener('resize', alignSidenotes);
```

Note: sidenote is *optional* per `SKILL.md` §2C — only include if user explicitly asks.

---

## 3. Footnotes (page-bottom)

Use these when sidenotes don't fit (mobile-heavy content, or many citations). The pattern is: superscript link → jumps to the bottom-of-page footnote → back-link returns to the original location.

```html
<p>This algorithm was first described by Polyak in 1964.<sup><a href="#fn-1" id="fnref-1" class="footnote-ref">1</a></sup></p>

<!-- At end of article -->
<section class="footnotes" aria-label="Footnotes">
  <h2 class="footnotes-label">Footnotes</h2>
  <ol class="footnotes-list">
    <li id="fn-1">
      Polyak, B. T. (1964). Some methods of speeding up the convergence of iteration methods. <em>USSR Comput. Math. & Math. Phys.</em>, 4(5), 1-17.
      <a href="#fnref-1" class="footnote-back" aria-label="Back to text">↩</a>
    </li>
  </ol>
</section>
```

```css
.footnote-ref {
  color: var(--accent-cyan);
  font-family: var(--font-mono);
  font-size: 0.7em;
  text-decoration: none;
  padding: 0 2px;
}
.footnote-ref::before { content: '['; }
.footnote-ref::after  { content: ']'; }
.footnote-ref:hover   { background: rgb(var(--accent-cyan-rgb) / 0.10); }

.footnotes {
  margin-top: var(--sp-16);
  padding-top: var(--sp-6);
  border-top: 1px solid var(--border);
}
.footnotes-label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin-bottom: var(--sp-4);
}
.footnotes-list {
  font-size: var(--fs-sm);
  color: var(--text-secondary);
  padding-left: var(--sp-5);
}
.footnotes-list li { margin-bottom: var(--sp-3); }
.footnotes-list li:target { background: rgb(var(--accent-yellow-rgb) / 0.08); }
.footnote-back {
  margin-left: var(--sp-2);
  color: var(--text-dim);
  text-decoration: none;
  font-family: var(--font-mono);
}
.footnote-back:hover { color: var(--accent-green); }
```

Note: footnotes are *required* when the page has citations; *optional* otherwise (per `SKILL.md` §2C).

---

## 4. Self-check / Quiz block

Single-answer multiple-choice or fill-in. Useful at the end of a section to lock in the concept.

```html
<div class="quiz" data-correct="b">
  <div class="quiz-prompt">
    <span class="quiz-label">CHECK YOURSELF</span>
    <p>Which of these guarantees gradient descent converges to the global minimum?</p>
  </div>
  <div class="quiz-options">
    <button class="quiz-opt" data-id="a">Sufficiently small learning rate</button>
    <button class="quiz-opt" data-id="b">Convexity of the loss function</button>
    <button class="quiz-opt" data-id="c">Initialization near the optimum</button>
    <button class="quiz-opt" data-id="d">Large enough batch size</button>
  </div>
  <div class="quiz-feedback" hidden>
    <p class="quiz-feedback-correct">
      <strong>Correct.</strong> Convexity guarantees no spurious local minima — every local min is global.
    </p>
    <p class="quiz-feedback-wrong">
      <strong>Not quite.</strong> Small learning rate, good init, and big batches all help in practice but none <em>guarantee</em> global convergence on a non-convex landscape.
    </p>
  </div>
</div>
```

```css
.quiz {
  margin: var(--sp-8) 0;
  padding: var(--sp-5) var(--sp-6);
  border: 1px solid var(--border);
  border-left: 3px solid var(--accent-cyan);
  border-radius: 0 var(--r-md) var(--r-md) 0;
  background: rgb(var(--accent-cyan-rgb) / 0.04);
}
.quiz-label {
  display: inline-block;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--accent-cyan);
  margin-bottom: var(--sp-2);
}
.quiz-options {
  display: flex; flex-direction: column; gap: var(--sp-2);
  margin: var(--sp-4) 0;
}
.quiz-opt {
  text-align: left;
  font-family: var(--font-body); font-size: var(--fs-sm);
  padding: var(--sp-3) var(--sp-4);
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-sm);
  color: var(--text-primary);
  cursor: pointer;
  transition: border-color var(--dur-fast), background var(--dur-fast);
}
.quiz-opt:hover:not(:disabled) {
  border-color: rgb(var(--accent-cyan-rgb) / 0.5);
}
.quiz-opt[data-state="correct"] {
  border-color: var(--accent-green);
  background: rgb(var(--accent-green-rgb) / 0.08);
  color: var(--accent-green);
}
.quiz-opt[data-state="wrong"] {
  border-color: var(--accent-red);
  background: rgb(var(--accent-red-rgb) / 0.08);
  color: var(--accent-red);
}
.quiz-opt:disabled { cursor: default; }

.quiz-feedback { margin-top: var(--sp-3); font-size: var(--fs-sm); }
.quiz-feedback-correct { color: var(--accent-green); }
.quiz-feedback-wrong   { color: var(--accent-red); }
```

```js
document.querySelectorAll('.quiz').forEach(quiz => {
  const correct = quiz.dataset.correct;
  const feedback = quiz.querySelector('.quiz-feedback');
  const correctMsg = quiz.querySelector('.quiz-feedback-correct');
  const wrongMsg   = quiz.querySelector('.quiz-feedback-wrong');

  quiz.querySelectorAll('.quiz-opt').forEach(btn => {
    btn.addEventListener('click', () => {
      const isCorrect = btn.dataset.id === correct;
      btn.dataset.state = isCorrect ? 'correct' : 'wrong';
      if (!isCorrect) {
        // also reveal the correct answer
        quiz.querySelector(`.quiz-opt[data-id="${correct}"]`).dataset.state = 'correct';
      }
      // disable all
      quiz.querySelectorAll('.quiz-opt').forEach(b => b.disabled = true);
      // show feedback
      feedback.hidden = false;
      correctMsg.style.display = isCorrect ? 'block' : 'none';
      wrongMsg.style.display   = isCorrect ? 'none'  : 'block';
    });
  });
});
```

Note: quiz is *optional* per `SKILL.md` §2C — only include if user explicitly asks.

---

## 5. Vocabulary highlight + glossary tooltip

Inline term that the reader can hover for a definition. Useful when introducing technical jargon without disrupting the prose.

```html
<p>The <span class="vocab" data-def="A vector pointing in the direction of steepest increase of a function.">gradient</span> at a point is computed by partial derivatives.</p>
```

```css
.vocab {
  position: relative;
  border-bottom: 1px dashed var(--accent-cyan);
  cursor: help;
  color: var(--text-primary);
}
.vocab::after {
  content: attr(data-def);
  position: absolute;
  bottom: 100%; left: 0;
  width: max-content; max-width: 280px;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-sm);
  padding: var(--sp-2) var(--sp-3);
  font-family: var(--font-body);
  font-size: var(--fs-xs);
  font-weight: normal;
  color: var(--text-secondary);
  line-height: 1.5;
  opacity: 0;
  transform: translateY(4px);
  transition: opacity var(--dur-fast), transform var(--dur-fast);
  pointer-events: none;
  z-index: var(--z-dropdown);
  /* Allowed shadow exception: floats above content */
  box-shadow: 0 8px 24px rgba(0,0,0,0.4);
}
.vocab:hover::after, .vocab:focus::after {
  opacity: 1;
  transform: translateY(0);
}
[data-theme="light"] .vocab::after {
  box-shadow: 0 8px 24px rgba(0,0,0,0.12);
}
```

For accessibility, also support keyboard focus by making the span focusable: `<span class="vocab" tabindex="0" data-def="...">`.

Note: vocab tooltip is *optional* per `SKILL.md` §2C — only include if user explicitly asks.

---

## 6. Pedagogy tie-in

These components serve specific roles in `foundations/pedagogy.md`'s framework. Match the tool to the job:

| Pedagogy goal (from `foundations/pedagogy.md`) | Use this component |
|---|---|
| §3 "Concrete before abstract" — show worked example before theorem | `.example` box (in `usecases/math-learning.md`) |
| §4 "Address misconception" — disarm wrong belief | `.warning` box (in `usecases/math-learning.md`) |
| §3 "Greek letter + number on next line" — anchor immediately | inline `$\alpha$` then example value |
| §8 "Tunable depth" — going-deeper option | `.collapsible[data-variant="aside"]` (this file §1) |
| §8 "Send the reader off" — end with question | `.quiz` for self-check, or trailing prompt sentence |
| §10 "19-year-old test" — annotate hard parts | `.vocab` for jargon, `.sidenote` for explanations (this file §2, §5) |

A page that uses all the layers — main prose, sidenotes for asides, collapsibles for "show me the proof", a quiz at the end, footnotes for citations — gives the reader exactly the depth they want, no more and no less. **But none of it is automatic** — every aside element is optional under the §2C protocol; user must ask.
