# Pedagogy — How to Think Before You Explain

Read this before writing any educational/explainer content. The visual design rules in `foundations/tokens.md` and `components/core.md` make a page *look* clear, but they don't make it *be* clear. This file is about reasoning, not styling.

The goal: a curious reader who didn't know the topic 10 minutes ago should be able to read your explanation once and walk away with both an intuition AND the formal definition — not one or the other.

**Quick map**: §1 The three failure modes of bad explanations (L9–41) · §2 The layered explanation pattern (L42–68) · §3 The concrete-first principle (L69–97) · §4 Address the misconception, don't pretend it doesn't exist (L98–115) · §5 The cognitive load budget (L116–143) · §6 Sentence-level prose patterns (L144–176) · §7 Visuals: when they help, when they hurt (L177–205) · §8 The completeness vs. minimalism tension (L206–232) · §9 Self-test before publishing (L233–251)

---

## 1. The three failure modes of bad explanations

Every unclear explanation falls into one of these. Diagnose which one your draft is at risk of.

### Failure mode 1: **Cognitive overload**
Too many new terms, definitions, and notations introduced in one section. Reader can't hold them in working memory.

> "A tensor is a generalization of a matrix to higher dimensions. The rank of a tensor is the number of dimensions. Tensors of rank 2 are matrices, rank 1 are vectors, rank 0 are scalars. Operations on tensors are defined via tensor products, contractions, and Einstein notation, which uses repeated indices to indicate summation across that dimension..."

By sentence 3, the reader has met 8 new terms. They've stopped reading.

**Fix**: introduce ≤ 3 new terms per section. Each term gets one sentence of intuition, one concrete example, then is *used* in the next paragraph (so it's reinforced).

### Failure mode 2: **Vagueness disguised as approachability**
The opposite extreme. Reader leaves with a vibe but no usable knowledge.

> "Gradient descent is a way for the algorithm to learn from data. It tries different values and picks the one that works best."

This says nothing wrong but also nothing operational. Reader can't recognize, apply, or extend the concept after reading.

**Fix**: every "intuition" sentence must be followed within the same section by something concrete: a formula, a number, a worked example, or a code snippet. Vibes pay rent by being followed by precision.

### Failure mode 3: **Formalism-first**
Drops the equation in paragraph 1, then spends the rest of the page deriving why it makes sense. Reader has nothing to anchor the symbols to.

> "$$ \theta_{t+1} = \theta_t - \alpha \nabla_\theta \mathcal{L}(\theta_t) $$ This is gradient descent, where $\theta$ is the parameter vector, $\alpha$ is the learning rate, and $\mathcal{L}$ is the loss..."

The reader sees Greek letters before they have any reason to care. They bounce.

**Fix**: never lead a section with a formula. Lead with the problem the formula solves. The formula appears as the **answer** to a question already in the reader's head.

---

## 2. The layered explanation pattern

For any non-trivial concept, write **three explanations** in sequence — each layer using vocabulary from the previous one:

### Layer 1: The cartoon
One sentence. No formulas. No jargon. Captures the soul of the idea.

> *Gradient descent is rolling a ball downhill until it stops.*

If you can't write this sentence, you don't yet understand the concept well enough to explain it. Go reread the source until you can.

### Layer 2: The mechanism
2-4 sentences. Names the moving parts. Says what each does. Still no formulas, but the words "gradient", "step", "loss" can appear.

> *At each step, we look at the slope of the loss function under our feet — the gradient. We take a step in the downhill direction. The step size is the learning rate. We repeat until the slope flattens, meaning we've found a minimum.*

### Layer 3: The formal statement
Now the formula. Now Greek letters. Now bounds and convergence.

> *Given a differentiable $f: \mathbb{R}^n \to \mathbb{R}$ and a starting point $x_0$, the gradient descent iteration is $x_{t+1} = x_t - \alpha \nabla f(x_t)$ with step size $\alpha > 0$. Convergence to a stationary point is guaranteed when $f$ is L-smooth and $\alpha < 2/L$.*

By the time you reach Layer 3, the reader already has hooks in their head for every term: "gradient" was named in Layer 2, "step" became $x_{t+1} - x_t$, "step size" became $\alpha$. The Greek letters are translations, not introductions.

**The discipline**: do not skip a layer. The temptation is to go straight from Layer 1 to Layer 3. The reader feels it.

---

## 3. The concrete-first principle

Mathematics teachers learned this the hard way. Always show one specific worked example before stating the general theorem.

### Bad

> Theorem: For any convex function $f$, the local minimum is also the global minimum.
> Proof: Suppose $x^*$ is local but not global...

### Good

> Take $f(x) = (x-3)^2$. Its minimum is at $x = 3$. There's no other "valley" anywhere — the function only has one bottom. This isn't an accident: convex functions can't have hidden valleys, and that fact has a name.
>
> **Theorem** (Convex local-global): For any convex function $f$, every local minimum is also a global minimum.

Same theorem. The "good" version takes 2x the words and 10x the comprehension.

**Rule**: every theorem, definition, or formula is preceded by a concrete instance. Not following — preceding.

### When introducing a Greek letter

The very next line after introducing a Greek letter must use it with a number. Not "let $\alpha$ be the learning rate" then 100 words of prose, then finally an example. Do this:

> *Let $\alpha$ be the learning rate. For instance, $\alpha = 0.05$ means each step is 5% of the gradient's magnitude.*

The reader's brain mints a new symbol. Anchor it immediately or you'll lose it.

---

## 4. Address the misconception, don't pretend it doesn't exist

For most concepts, learners arrive with a wrong mental model. If you don't acknowledge and disarm it, your explanation lands on top of the wrong model and gets distorted.

### Common misconceptions to disarm explicitly

| Topic | Common misconception | What to say |
|---|---|---|
| Gradient descent | "It always finds the global minimum." | Only for convex functions. Otherwise it finds a local minimum that depends on initialization. |
| Probability | "$P(A \mid B) = P(B \mid A)$." | These are different in general. Bayes' theorem relates them. |
| Statistics | "p-value is the probability the null hypothesis is true." | It's the probability of seeing data this extreme *if* the null were true. Different thing. |
| Neural networks | "More layers always means better." | Past a certain depth, gradients vanish/explode and training becomes unstable without normalization. |
| Big-O notation | "Lower constant always means faster in practice." | Constants matter for small inputs; quicksort beats mergesort in practice despite same Big-O. |

**Pattern**: when you teach concept X, ask yourself "what's the wrong thing someone might already believe?" If the answer is non-trivial, address it in a `.warning` box before introducing the right model.

---

## 5. The cognitive load budget

A reader has finite attention per section. Spend it carefully.

### Per section budget

- **≤ 3 new terms** introduced as named concepts (with formal name)
- **≤ 1 new formula** as the section's centerpiece
- **≤ 2 worked examples** demonstrating the formula
- **≥ 1 visual** if the concept has any spatial/dynamic structure

If you have more, split the section.

### Per page budget

- **≤ 1 main concept** explained per page. Sub-concepts that *enable* the main concept are fine; competing concepts are not.
- **≥ 1 interactive element** if the page is longer than ~800 words and the concept is dynamic
- **≤ 7 numbered equations** total (more = reference-card territory, not explainer)

### Density warning signs

- "First, recall that..." in paragraph 1 → reader probably hasn't recalled it. Either restate or link out.
- More than 2 unfamiliar Greek letters in one paragraph → split.
- Reader has to scroll back to remember what a symbol means → introduce it again or replace with words.
- A sentence with 3+ technical terms, none recently defined → rewrite.

---

## 6. Sentence-level prose patterns

The page-structure rules above are necessary but not sufficient. Bad sentences ruin good structure.

### Patterns that fail

- **"It is well known that..."** — assumes shared knowledge that may not exist. State it instead.
- **"Trivially..."** — alienates anyone for whom it's not trivial. Just say the step.
- **"Note that..."** — usually flags a step you haven't justified. Justify it instead.
- **"Clearly..."** — same problem.
- **"It can be shown that..."** — code for "I don't want to prove this." Either prove it or cite the proof.

### Patterns that work

- **Lead with the takeaway**: first sentence of every paragraph states what the paragraph teaches. The rest justifies it.
- **One thought per sentence**: long sentences with multiple clauses force the reader to re-parse. Cut them.
- **Active voice for actions**: "we compute X" not "X is computed". Reader is along for the ride.
- **Numbers, not adjectives**: not "very fast", but "200ms latency". Not "much smaller", but "about 1/100th the size".
- **Tense consistency**: explainers are usually in present tense. ("We compute the gradient. The algorithm steps in the direction of...") Don't drift to past or future.

### The "rewrite for the reader" pass

After your first draft, read each sentence and ask: *"Why is this here?"* If the answer is "to sound thorough" or "because it's true", cut it. Every sentence should either:

1. Convey a fact the reader needs
2. Build intuition for something coming up
3. Acknowledge and disarm a misconception
4. Connect to what was just said

If a sentence does none of these, it's noise.

---

## 7. Visuals: when they help, when they hurt

Not every concept needs a picture. Bad pictures make explanations worse.

### Use a visual when

- The concept has spatial structure (geometry, topology, network layers)
- The concept has dynamics (trajectories, gradient flow, signal propagation)
- The concept is a relationship (A vs B, before vs after, distribution comparison)
- A formula has hidden meaning that geometry exposes (e.g., Lagrange multipliers as tangent contact)

### Skip the visual when

- The concept is a single discrete fact ("Big-O of quicksort is $O(n \log n)$" — no diagram needed)
- The concept is recursive but tree-structured doesn't help understanding (recursion is sometimes clearer in code than in a tree picture)
- You can't think of a meaningful visual after 5 minutes — meaning the visual would be decorative, not pedagogical

### Required parts of any pedagogical visual

Per `foundations/plot-quality.md`. Quick summary:
- Axis labels with units
- A title or eyebrow telling the reader what to look at first
- An annotation pointing to the salient feature ("← optimum here", "← divergence at this α")
- A caption: setup + what to notice + caveat

A pretty unlabeled chart is just decoration.

---

## 8. The completeness vs. minimalism tension

The user asked: *"đầy đủ, đúng nhất"* — complete and correct. But complete in what sense?

**Resolution: layer the depth, not the breadth.**

The first read should be **complete in breadth**: every concept the reader needs to understand the page is on the page. No assumed prerequisites the reader can't easily look up.

Each concept's **depth** should be tunable:
- Default depth: enough to use the concept correctly
- Deeper: a `.note` or `.example` box for "going deeper" (optional read)
- Deepest: a citation to the original paper or textbook chapter

So the page is *complete* (no gaps) but *short* (no unnecessary depth). A motivated reader can drill down via citations; a casual reader gets the working knowledge.

### The "send the reader off" rule

End every page with one of:

1. **A question** the reader can now think about ("What happens if you set $\beta = 0.99$?")
2. **A pointer** to where this concept connects next ("This is the foundation for Adam, which adds...")
3. **A citation** to the canonical source ("For the full proof, see Polyak 1964, §4.")

Don't end with "In summary, gradient descent moves in the negative gradient direction." Reader knew this from your title; the recap is wasted.

---

## 9. Self-test before publishing

After writing a draft, run these checks:

- [ ] **Layer test**: Does the page contain Layer 1 (cartoon) before Layer 3 (formula)?
- [ ] **Concrete test**: Is there a worked numerical example before the general theorem?
- [ ] **Greek letter test**: Is every Greek letter used with a number on the next line after introduction?
- [ ] **Misconception test**: What's the wrong belief a reader might arrive with? Did you address it?
- [ ] **Cognitive load test**: ≤3 new terms per section? ≤1 main concept per page?
- [ ] **Reader-ahead test**: Pick any paragraph; can the reader predict roughly what the next paragraph will say from the topic sentence?
- [ ] **Cut test**: Can you delete any paragraph without losing core meaning? If yes, delete it.
- [ ] **End test**: Does the page end with a question, a pointer, or a citation — not a recap?

If all 8 pass, you've earned the reader's time.

### The 19-year-old test (the meta-test)

Imagine a curious 19-year-old undergraduate who knows the prerequisites listed at the top but has never seen this specific concept. Reading as them: where do you get confused (too dense), where do you skim (redundant prose), where do you stop (lost), what do you remember after closing the tab (the actual lesson). If the last answer doesn't include your main concept, the page failed regardless of how nice the design looks. This is the only test that ultimately matters; the 8 above are operational subgoals of it.
