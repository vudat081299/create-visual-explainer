# Interactive Explainer Patterns

Patterns for "play with this concept" pages — the genre pioneered by Distill.pub, 3Blue1Brown, Brilliant.org, Bartosz Ciechanowski. Read this when the user wants to explain a concept (math, ML, physics, algorithms) in a way that lets the reader *interact* rather than just *read*.

The aesthetic goal: a reader can **immediately see** what the concept does, then peek at the formalism if they want. Slider on top, equation underneath — never the other way around.

**Quick map**: §1 Pedagogical structure (load order before code) (L11–27) · §2 Stage + controls layout (core pattern) (L28–140) · §3 Code-output split panes (L141–191) · §4 Annotated diagram pattern (L192–256) · §5 Step-through walkthroughs (L257–328) · §6 Comparison panels (A/B, before/after) (L329–386) · §7 "Try it yourself" sandbox (L387–451) · §8 Reader-attention guidelines (L452–462)

---

## 1. Pedagogical structure (apply this BEFORE writing any code)

Every explainer page should follow this skeleton. Skip steps and the page becomes either a wall of formulas (boring) or a pile of toys without theory (vague).

| Section | Purpose | Components used |
|---|---|---|
| **Hook** (1-2 paragraphs) | What's the concept? Why does anyone care? Plain English, no math. | `.page-head` only |
| **Intuition viz** | Show the thing moving. The reader plays with one parameter. | `.explainer-stage` + `.explainer-controls` |
| **Naming what just happened** | Tie the visual to formal terms. Each term gets one sentence. | `.definition` boxes |
| **The formalism** | The actual equation. Now it's grounded — reader has seen it. | `.equation`, `.theorem` |
| **Edge cases / failure modes** | What breaks? When does this concept stop working? | `.warning`, `.example` |
| **Synthesis** | One paragraph: how do the pieces fit together? Send the reader off. | plain prose |

**The hardest discipline**: don't put the equation first. Even if the equation is the cleanest summary, the reader hasn't earned it yet — show them the moving picture, *then* hand them the symbol.

---

## 2. Stage + controls layout (the core pattern)

The single most-used component in explainers: a visualization on top, controls beneath. Reader drags a slider → the viz updates immediately.

```html
<section class="explainer">
  <div class="explainer-stage" id="stage">
    <!-- canvas / svg / iframe goes here -->
  </div>

  <div class="explainer-controls">
    <div class="control-group">
      <label for="learning-rate">LEARNING RATE</label>
      <input type="range" class="slider" id="learning-rate"
             min="0.001" max="1" step="0.001" value="0.1">
      <output class="control-value" for="learning-rate">0.100</output>
    </div>

    <div class="control-group">
      <label for="momentum">MOMENTUM</label>
      <input type="range" class="slider" id="momentum"
             min="0" max="0.99" step="0.01" value="0.9">
      <output class="control-value" for="momentum">0.90</output>
    </div>

    <button class="btn btn-secondary btn-sm" id="reset">Reset</button>
  </div>

  <p class="explainer-caption">
    Drag <strong>learning rate</strong> to see why too-large values overshoot the minimum.
  </p>
</section>
```

```css
.explainer {
  margin: var(--sp-12) 0;
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  overflow: hidden;
  background: var(--card);
}

.explainer-stage {
  width: 100%;
  aspect-ratio: 16 / 9;
  background: var(--bg);
  border-bottom: 1px solid var(--border);
  position: relative;
  isolation: isolate;
}

.explainer-controls {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: var(--sp-5);
  padding: var(--sp-5) var(--sp-6);
  align-items: end;
}

.control-group {
  display: flex; flex-direction: column;
  gap: var(--sp-2);
}

.control-group label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
}

.control-value {
  font-family: var(--font-mono);
  font-size: var(--fs-base);
  color: var(--accent-green);
  font-variant-numeric: tabular-nums;
  align-self: flex-end;
}

.explainer-caption {
  padding: var(--sp-4) var(--sp-6);
  font-size: var(--fs-sm);
  color: var(--text-secondary);
  border-top: 1px solid var(--border);
  background: var(--surface);
}
.explainer-caption strong {
  color: var(--accent-green);
  font-weight: 500;
}
```

### Wiring it up (vanilla JS)

```js
const slider = document.getElementById('learning-rate');
const output = document.querySelector('output[for="learning-rate"]');

slider.addEventListener('input', () => {
  const v = parseFloat(slider.value);
  output.textContent = v.toFixed(3);
  redrawStage(v);                                     // reader-visible feedback
});

function redrawStage(learningRate) { /* update canvas/svg */ }
```

**Always echo the slider value as text** (`<output>` element). Two reasons: a) the reader can read the exact value, not just see the position; b) screen readers announce it.

---

## 3. Code-output split panes

For "look at what this code does" explainers (algorithm walkthroughs, ML model demos).

```html
<div class="split-pane">
  <div class="split-code">
    <div class="code-block" data-lang="python">
      <pre><code>def sigmoid(x):
    return 1 / (1 + np.exp(-x))

x = np.linspace(-6, 6, 100)
y = sigmoid(x)
plt.plot(x, y)</code></pre>
    </div>
  </div>

  <div class="split-output">
    <canvas id="sigmoid-plot"></canvas>
  </div>
</div>
```

```css
.split-pane {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--sp-4);
  margin: var(--sp-8) 0;
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  overflow: hidden;
}

.split-code,
.split-output {
  padding: var(--sp-5);
  background: var(--card);
}

.split-code   { border-right: 1px solid var(--border); background: var(--surface); }
.split-output { display: grid; place-items: center; }

@media (max-width: 768px) {
  .split-pane { grid-template-columns: 1fr; }
  .split-code { border-right: none; border-bottom: 1px solid var(--border); }
}
```

---

## 4. Annotated diagram pattern

For pointing at parts of an image/SVG and labeling them with arrows or callouts.

```html
<figure class="annotated">
  <div class="annotated-stage">
    <svg viewBox="0 0 600 400">
      <!-- the diagram -->
    </svg>
    <span class="callout" style="left:25%; top:35%;">
      <span class="callout-num">1</span>
      <span class="callout-text">Input layer — raw pixel values</span>
    </span>
    <span class="callout" style="left:55%; top:55%;">
      <span class="callout-num">2</span>
      <span class="callout-text">Hidden layer — learned features</span>
    </span>
  </div>
  <figcaption>Forward pass through a 2-layer network.</figcaption>
</figure>
```

```css
.annotated { margin: var(--sp-8) 0; }
.annotated-stage {
  position: relative;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: var(--sp-5);
}
.callout {
  position: absolute;
  display: flex; align-items: center; gap: var(--sp-2);
  background: var(--card);
  border: 1px solid rgb(var(--accent-yellow-rgb) / 0.4);
  border-radius: var(--r-pill);
  padding: 4px var(--sp-3) 4px 4px;
  font-size: var(--fs-xs);
  color: var(--text-primary);
}
.callout-num {
  display: grid; place-items: center;
  width: 22px; height: 22px;
  background: var(--accent-yellow);
  color: var(--bg);
  border-radius: 50%;
  font-family: var(--font-mono);
  font-weight: 700;
  font-size: var(--fs-xs);
}
.annotated figcaption {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin-top: var(--sp-3);
  text-align: center;
}
```

---

## 5. Step-through walkthroughs

For algorithms or proofs where the reader advances one step at a time.

```html
<div class="walkthrough">
  <div class="walkthrough-nav">
    <button class="btn btn-ghost btn-sm" id="prev">← Prev</button>
    <span class="walkthrough-progress">STEP <strong>3</strong> OF 8</span>
    <button class="btn btn-secondary btn-sm" id="next">Next →</button>
  </div>

  <div class="walkthrough-stage">
    <!-- Current step viz -->
  </div>

  <div class="walkthrough-explanation">
    <span class="step-label">STEP 03 — PARTITIONING</span>
    <p>Now we pick a pivot — the rightmost element — and rearrange the array so everything smaller sits to its left.</p>
  </div>
</div>
```

```css
.walkthrough {
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  overflow: hidden;
  margin: var(--sp-8) 0;
}
.walkthrough-nav {
  display: flex; align-items: center; justify-content: space-between;
  padding: var(--sp-3) var(--sp-5);
  background: var(--surface);
  border-bottom: 1px solid var(--border);
}
.walkthrough-progress {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  color: var(--text-muted);
}
.walkthrough-progress strong {
  color: var(--accent-green);
  font-weight: 500;
  font-variant-numeric: tabular-nums;
}
.walkthrough-stage {
  aspect-ratio: 16 / 9;
  background: var(--bg);
  position: relative;
  isolation: isolate;
}
.walkthrough-explanation {
  padding: var(--sp-5) var(--sp-6);
  background: var(--card);
}
```

### Keyboard navigation (mandatory)

```js
document.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowRight') document.getElementById('next').click();
  if (e.key === 'ArrowLeft')  document.getElementById('prev').click();
});
```

Walkthroughs without arrow-key nav feel broken. Always wire this up.

---

## 6. Comparison panels (A/B, before/after, with/without)

```html
<div class="comparison">
  <div class="comparison-side">
    <span class="comparison-label" data-variant="a">WITHOUT REGULARIZATION</span>
    <canvas></canvas>
    <span class="stat-value" style="color:var(--accent-red)">23.1%</span>
    <span class="stat-label">Test error</span>
  </div>
  <div class="comparison-divider" aria-hidden="true"></div>
  <div class="comparison-side">
    <span class="comparison-label" data-variant="b">WITH L2 (λ=0.01)</span>
    <canvas></canvas>
    <span class="stat-value" style="color:var(--accent-green)">8.4%</span>
    <span class="stat-label">Test error</span>
  </div>
</div>
```

```css
.comparison {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  gap: 0;
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  overflow: hidden;
  background: var(--card);
}
.comparison-side {
  padding: var(--sp-6);
  display: flex; flex-direction: column;
  align-items: flex-start;
  gap: var(--sp-3);
}
.comparison-label {
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
}
.comparison-label[data-variant="a"] { color: var(--accent-red); }
.comparison-label[data-variant="b"] { color: var(--accent-green); }
.comparison-divider {
  width: 1px;
  background: var(--border);
}
.comparison-side .stat-value { font-size: 56px; }

@media (max-width: 768px) {
  .comparison { grid-template-columns: 1fr; }
  .comparison-divider { width: 100%; height: 1px; }
}
```

---

## 7. "Try it yourself" sandbox

Most ambitious form — full editable code that runs live. Use sparingly (one per page max), and only when the concept truly requires hands-on tweaking.

Browser-side JS sandboxes: just `eval()` user input in a try/catch, render output. For Python: use Pyodide.

```html
<div class="sandbox">
  <div class="sandbox-header">
    <span class="eyebrow" style="color:var(--accent-cyan)">TRY IT YOURSELF</span>
    <button class="btn btn-primary btn-sm" id="run">▶ Run</button>
  </div>
  <textarea class="sandbox-editor" spellcheck="false" rows="8">
// Try changing the activation function
function activate(x) {
  return Math.tanh(x);
}
plot(activate);
  </textarea>
  <div class="sandbox-output" id="output"></div>
</div>
```

```css
.sandbox {
  border: 1px solid rgb(var(--accent-cyan-rgb) / 0.3);
  border-radius: var(--r-lg);
  overflow: hidden;
  margin: var(--sp-8) 0;
}
.sandbox-header {
  display: flex; align-items: center; justify-content: space-between;
  padding: var(--sp-3) var(--sp-5);
  background: rgb(var(--accent-cyan-rgb) / 0.06);
  border-bottom: 1px solid rgb(var(--accent-cyan-rgb) / 0.2);
}
.sandbox-editor {
  width: 100%;
  background: var(--surface);
  color: var(--text-primary);
  font-family: var(--font-mono);
  font-size: var(--fs-code);
  line-height: 1.6;
  padding: var(--sp-5);
  border: none;
  outline: none;
  resize: vertical;
  min-height: 160px;
}
.sandbox-output {
  background: var(--card);
  padding: var(--sp-5);
  min-height: 120px;
  border-top: 1px solid var(--border);
}
.sandbox-output:empty::before {
  content: 'Output appears here after running.';
  color: var(--text-dim);
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
}
```

---

## 8. Reader-attention guidelines

These are content-level rules. The design supports them, but the writer has to apply them.

1. **One concept per page.** If two concepts need explaining, two pages. Don't dump.
2. **Every interactive element earns its weight.** A slider that does nothing teach-able is noise. Cut it.
3. **Default the slider to a "nice" value.** If the parameter range is `0.001` to `1`, default to something visually informative (e.g. `0.1`), not the midpoint.
4. **Annotate the moving parts.** The first time the visualization moves, drop a `.callout` on the part that just changed. Remove it after a few seconds or after the reader interacts.
5. **Prose around the viz, not after it.** Reader should be reading and dragging at the same time, not scrolling to find the explanation.
6. **End with a question, not a summary.** "What happens if you crank momentum to 0.99? Try it." invites further play; "In summary, momentum smooths gradient updates" closes the door.
