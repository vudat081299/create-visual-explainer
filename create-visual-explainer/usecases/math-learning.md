# Math / DS Learning Patterns

Patterns for math-heavy or data-science educational content: theorems, definitions, proofs, equations, derivations. Read this when the user is producing tutorials, lecture notes, textbook-style content, or any document with significant LaTeX content.

For interactive explainers (sliders that drive a visualization, live demos), also read `usecases/interactive-explainer.md`.

**Quick map**: §1 KaTeX setup (L11–37) · §2 Reading-optimized container (L38–86) · §3 Equation containers (L87–129) · §4 Theorem / Definition / Proof boxes (L130–247) · §5 Inline math conventions (L248–259) · §6 Step-by-step derivations (L260–325) · §7 Pedagogy reminders for math content (L326–329)

---

## 1. KaTeX setup

KaTeX is preferred over MathJax: 10× faster render, no flash-of-unstyled-math, and the typography fits the system better.

```html
<link rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
<script defer
        src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
<script defer
        src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/contrib/auto-render.min.js"
        onload="renderMathInElement(document.body, {
          delimiters: [
            {left: '$$', right: '$$', display: true},
            {left: '$',  right: '$',  display: false},
            {left: '\\[', right: '\\]', display: true},
            {left: '\\(', right: '\\)', display: false}
          ],
          throwOnError: false
        })"></script>
```

Inline math: `$\nabla f(x) = 0$` → renders flush with body text.
Display math: `$$\nabla f(x) = 0$$` → gets the bordered card from §3.

---

## 2. Reading-optimized container

Math content benefits from narrower columns. Long lines kill comprehension on equations.

```html
<article class="prose-container">
  <header class="page-head">...</header>
  <p>...</p>
  $$\text{display equation}$$
  <div class="theorem">...</div>
</article>
```

```css
.prose-container {
  max-width: 68ch;        /* ~620px at base font — good for both prose and math */
  margin-inline: auto;
  padding-inline: var(--sp-4);
}

.prose-container p,
.prose-container li {
  font-size: var(--fs-base);
  line-height: 1.7;        /* slightly looser than default 1.5 for math-mixed text */
  color: var(--text-primary);
}

.prose-container > * + * {
  margin-top: var(--sp-5);   /* vertical rhythm */
}

.prose-container h2 {
  font-family: var(--font-display);
  font-size: var(--fs-h2);
  letter-spacing: 0.01em;
  margin-top: var(--sp-12);
  margin-bottom: var(--sp-3);
}

.prose-container h3 {
  font-family: var(--font-display);
  font-size: var(--fs-h3);
  margin-top: var(--sp-8);
  margin-bottom: var(--sp-2);
}
```

---

## 3. Equation containers

Block equations get a left-border-accent card. The blue is the default; subtype variants use other accents.

```css
.katex-display,
.equation {
  margin: var(--sp-6) 0;
  padding: var(--sp-5) var(--sp-6);
  background: var(--card);
  border-left: 3px solid var(--accent-blue);
  border-radius: 0 var(--r-md) var(--r-md) 0;
  overflow-x: auto;
}

.katex { color: var(--text-primary); font-size: 1.05em; }

/* Equation with right-aligned numeric label: (3.4) */
.equation {
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: center;
  gap: var(--sp-4);
}

.eq-num {
  font-family: var(--font-mono);
  font-size: var(--fs-xs);
  color: var(--text-muted);
  white-space: nowrap;
}
```

**Usage** (HTML pattern):
```html
<div class="equation">
  $$ \mathcal{L}(\theta) = \frac{1}{N} \sum_{i=1}^{N} (y_i - f_\theta(x_i))^2 $$
  <span class="eq-num">(3.4)</span>
</div>
```

---

## 4. Theorem / Definition / Proof boxes

The textbook-style "callout boxes". Each type has its own accent color so a reader can scan a page and quickly identify what's a definition vs. a theorem vs. a proof.

```css
.theorem, .proposition, .lemma, .corollary,
.definition, .proof, .example,
.note, .tip, .warning {
  margin: var(--sp-5) 0;
  padding: var(--sp-4) var(--sp-5);
  border-left: 3px solid;
  border-radius: 0 var(--r-md) var(--r-md) 0;
}

/* Theorem family — blue (formal claim) */
.theorem, .proposition, .lemma, .corollary {
  background: rgb(var(--accent-blue-rgb) / 0.06);
  border-color: var(--accent-blue);
}

/* Definition — green (foundational concept) */
.definition {
  background: rgb(var(--accent-green-rgb) / 0.06);
  border-color: var(--accent-green);
}

/* Proof — yellow (the work) */
.proof {
  background: rgb(var(--accent-yellow-rgb) / 0.06);
  border-color: var(--accent-yellow);
}

/* Example — purple (worked instance) */
.example {
  background: rgb(var(--accent-purple-rgb) / 0.06);
  border-color: var(--accent-purple);
}

/* Note / Tip — cyan (commentary) */
.note, .tip {
  background: rgb(var(--accent-cyan-rgb) / 0.05);
  border-color: var(--accent-cyan);
}

/* Warning — red (caveats, edge cases) */
.warning {
  background: rgb(var(--accent-red-rgb) / 0.06);
  border-color: var(--accent-red);
}
```

### Box labels (mono uppercase)

Each box opens with an eyebrow-style label. Use mono font, uppercase, with the box's accent color.

```css
.theorem-label, .definition-label, .proof-label,
.example-label, .note-label, .tip-label, .warning-label,
.proposition-label, .lemma-label, .corollary-label {
  display: inline-block;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  margin-bottom: var(--sp-2);
}

.theorem-label, .proposition-label, .lemma-label, .corollary-label { color: var(--accent-blue); }
.definition-label  { color: var(--accent-green); }
.proof-label       { color: var(--accent-yellow); }
.example-label     { color: var(--accent-purple); }
.note-label, .tip-label { color: var(--accent-cyan); }
.warning-label     { color: var(--accent-red); }

/* End-of-proof box (∎) — automatic, right-aligned */
.proof::after {
  content: '∎';
  float: right;
  color: var(--accent-yellow);
  font-size: 1.2em;
  margin-top: var(--sp-2);
}
```

### Usage (HTML pattern)

```html
<div class="definition">
  <span class="definition-label">DEFINITION 2.1 (Convex function)</span>
  <p>A function $f: \mathbb{R}^n \to \mathbb{R}$ is <strong>convex</strong> if for all $x, y \in \mathbb{R}^n$ and $\lambda \in [0,1]$:</p>
  $$ f(\lambda x + (1-\lambda) y) \leq \lambda f(x) + (1-\lambda) f(y) $$
</div>

<div class="theorem">
  <span class="theorem-label">THEOREM 2.4 (Jensen's inequality)</span>
  <p>For a convex function $f$ and a random variable $X$ with finite expectation:</p>
  $$ f(\mathbb{E}[X]) \leq \mathbb{E}[f(X)] $$
</div>

<div class="proof">
  <span class="proof-label">PROOF</span>
  <p>By induction on the support of $X$. Start with the case where $X$ takes two values...</p>
</div>
```

### Markdown extension mapping (if rendering MD)

If using a markdown processor with directive support (e.g. `markdown-it-container`), map:

| Markdown directive | Renders to |
|---|---|
| `:::theorem [Pythagoras]` ... `:::` | `<div class="theorem"><span class="theorem-label">THEOREM (Pythagoras)</span> ...` |
| `:::definition [Convex function]` | `<div class="definition">...` |
| `:::proof` | `<div class="proof">...` ending in `∎` |
| `:::note`, `:::tip`, `:::warning`, `:::example` | corresponding boxes |

---

## 5. Inline math conventions

When mixing math with prose, follow these rules to keep typography consistent:

- **Variables and Greek letters in body text** → wrap in `$...$`. Don't use `<i>` or `<em>` for variables. `Let $\theta$ denote the parameter` ✓ vs. `Let θ denote the parameter` ✗
- **Numbers without units** → fine in plain prose.
- **Numbers with units** → mono span: `<span class="numeric">94.2%</span>` for tabular alignment.
- **Function names from code** → use `<code>`, not math: `the <code>numpy.linalg.solve()</code> function`.
- **Algorithm names** → small caps in body font: `<span class="algo">SGD</span>` with `font-variant-caps: all-small-caps`.

---

## 6. Step-by-step derivations

Numbered derivations look better with an explicit grid, not just a list of equations.

```html
<div class="derivation">
  <ol>
    <li>
      <span class="step-label">Start from definition</span>
      $$ \mathcal{L}(\theta) = -\sum_{i=1}^N \log p_\theta(y_i \mid x_i) $$
    </li>
    <li>
      <span class="step-label">Apply chain rule</span>
      $$ \nabla_\theta \mathcal{L} = -\sum_{i=1}^N \nabla_\theta \log p_\theta(y_i \mid x_i) $$
    </li>
    <li>
      <span class="step-label">Substitute softmax</span>
      $$ \nabla_\theta \mathcal{L} = -\sum_{i=1}^N \left( \nabla_\theta z_{y_i} - \sum_k \frac{e^{z_k}}{\sum_j e^{z_j}} \nabla_\theta z_k \right) $$
    </li>
  </ol>
</div>
```

```css
.derivation ol {
  list-style: none;
  counter-reset: step;
  padding: 0;
}
.derivation ol > li {
  counter-increment: step;
  display: grid;
  grid-template-columns: auto 1fr;
  gap: var(--sp-3) var(--sp-5);
  align-items: start;
  padding: var(--sp-4) 0;
  border-bottom: 1px solid var(--border);
}
.derivation ol > li::before {
  content: counter(step, decimal-leading-zero);
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  color: var(--accent-yellow);
  letter-spacing: 0.1em;
  align-self: start;
  padding-top: 4px;
}
.step-label {
  grid-column: 2;
  font-family: var(--font-mono);
  font-size: var(--fs-label);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
  display: block;
  margin-bottom: var(--sp-2);
}
.derivation ol > li .equation,
.derivation ol > li .katex-display {
  grid-column: 2;
  margin: 0;
}
```

---

## 7. Pedagogy reminders for math content

The aesthetic rules above won't save bad pedagogy. For the structural patterns — layered explanation (cartoon → mechanism → formula), worked example before theorem, misconception warning, cognitive-load budget — see `foundations/pedagogy.md`. Cite sources via `.meta` line; cross-reference equations by number (`as shown in equation (3.4)`). The design system supports these through the box variants (`.theorem`, `.example`, `.warning`) and equation numbering — but the *content discipline* lives in `foundations/pedagogy.md` and `foundations/verification.md`, not here.
