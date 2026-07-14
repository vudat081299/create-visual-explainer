# Verification — The Pre-Output Protocol

Read this whenever the page contains math, formulas, derivations, code, citations, or factual claims about how a system behaves. The protocol is **before output**, not after. Errors caught here cost seconds; errors that ship cost the reader's trust.

The user's stated preference: *"Luôn verify lại tính xác thực, số liệu của các tài liệu trước khi gửi cho tôi và luôn reply kèm 1 dòng thể hiện là bạn đã verify lại tài liệu."* This file describes how to do the verification properly, not just claim to.

**Quick map**: §1 The pre-output checklist (L9–43) · §2 Math verification — the basics (L44–94) · §3 Numerical sanity checks (L95–120) · §4 Notation consistency (L121–145) · §5 Citation honesty (L146–173) · §6 Code verification (L174–217) · §7 The uncertainty admission (L218–246) · §8 The verification trail (chat-side, not artifact-side) (L247–268) · §9 The minimum viable verification (L269–283)

---

## 1. The pre-output checklist

Before sending any educational/technical content, walk through these. Don't skip.

### Math content
- [ ] Every derivative computed by hand and re-checked
- [ ] Every claimed numerical value plugged into the formula and verified
- [ ] Every limit case checked ($x \to 0$, $\alpha \to 0$, etc.)
- [ ] Every claimed bound (Lipschitz constant, convergence rate) derived, not memorized
- [ ] Units check on any physical/dimensional quantity

### Code content
- [ ] Every code snippet traced through manually with a small example
- [ ] Every claimed output of a snippet computed by hand
- [ ] Every API call verified against actual current API (not from memory if framework version matters)
- [ ] Edge cases considered: empty input, single element, very large input

### Factual / citation content
- [ ] Every named theorem verified to exist with that name
- [ ] Every paper citation has author + year + venue stated correctly, OR not cited at all
- [ ] Every "this was proven in [year]" claim rechecked, OR softened to "around the [decade]"
- [ ] Every empirical claim ("X% of practitioners use...") either sourced or removed

### Visual content
- [ ] Every plot's axes verified to match the data being plotted
- [ ] Every annotation verified against actual data ("← peak at 47" → really at epoch 47?)
- [ ] Every claimed feature in the caption visible in the rendered plot

### Structure / layout
- [ ] The page's macro-layout matches the knowledge's intrinsic shape (`foundations/structure.md`) — a process isn't laid out as an unordered set, a comparison isn't split into sequential essays, a hierarchy isn't flattened into a peer list
- [ ] A reader inferring the relationship between the parts *from the layout alone* would infer the true one — the silent structure self-check; it produces no line in the reply

### Notation / consistency
- [ ] Every symbol used means exactly one thing on the page
- [ ] Every Greek letter introduced has a stated role
- [ ] Vietnamese vs. English math conventions chosen and applied consistently (decimal `,` vs `.`, `\mathbb{R}` for reals, etc.)

---

## 2. Math verification — the basics

The most common math errors in AI-generated explanations.

### Sign errors

Especially in derivatives, Lagrangians, gradients, and physics formulas.

> *Claimed*: $\nabla f$ for $f(x,y) = x^2 + 5y^2$ is $(2x, 10y)$.
>
> *Verify*: $\partial f/\partial x = 2x$ ✓, $\partial f/\partial y = 10y$ ✓. Correct.

> *Claimed*: gradient descent step is $x_{t+1} = x_t + \alpha \nabla f$.
>
> *Verify*: gradient *ascent* would be `+`. Descent is `-`. **Sign error.**

Always re-derive, never copy from memory.

### Constant errors

Integration constants, eigenvalue computations, sign of trace, dimension factors. These flip easily.

> *Claimed*: Lipschitz constant of $\nabla f$ for $f = x^2 + 5y^2$ is $L = 5$.
>
> *Verify*: $L$ = max eigenvalue of Hessian. Hessian $= \text{diag}(2, 10)$. Max eigenvalue = 10. **Off by factor of 2.** Correct value is $L = 10$.

If you'll claim a numerical bound, compute it on paper first.

### Limit checks

Every formula should degenerate sensibly at boundary cases. If it doesn't, the formula is wrong or the boundary needs explicit handling.

> *Formula*: heavy-ball momentum $v_{t+1} = \beta v_t + \nabla f$.
>
> *Limit check $\beta \to 0$*: $v_{t+1} = \nabla f$, so update is $x_{t+1} = x_t - \alpha \nabla f$ — vanilla gradient descent ✓.
> *Limit check $\beta \to 1$*: $v_t$ accumulates without decay; can blow up. Caveat to mention in text.

If a limit case fails sanity, say so or fix the formula.

### Unit / dimension check

Especially for physics, finance, ML hyperparameters.

> *Claimed*: gradient descent stable when $\alpha < 2/L$.
>
> *Units check*: $\alpha$ is in [parameter units] / [gradient magnitude units] = [parameter] / ([function units] / [parameter units]) = [parameter]² / [function]. Lipschitz $L$ has units [function] / [parameter]². So $1/L$ has units [parameter]² / [function]. Same units as $\alpha$ ✓. Bound is dimensionally consistent.

If units don't match, the formula is wrong.

---

## 3. Numerical sanity checks

Whenever you state a number, plug it back in.

### Pattern: claim → verify

> *Claim*: With $f(x,y) = x^2 + 5y^2$ and starting point $(-2.5, 1.2)$, gradient descent with $\alpha = 0.05$ takes about 50 iterations to reach $\|x\| < 0.01$.
>
> *Verify*: 
> - Initial gradient: $(2 \cdot -2.5, 10 \cdot 1.2) = (-5, 12)$.
> - After step 1: $x = -2.5 - 0.05 \cdot (-5) = -2.25$. $y = 1.2 - 0.05 \cdot 12 = 0.6$.
> - The y-coordinate halves each step (factor $1 - 0.05 \cdot 10 = 0.5$). After $n$ steps: $y_n \approx 1.2 \cdot 0.5^n$.
> - For $|y| < 0.01$: $0.5^n < 0.01/1.2$, so $n > \log_2(120) \approx 6.9$. So 7 steps for y to converge.
> - The x-coordinate decays as $0.9^n$ (factor $1 - 0.05 \cdot 2 = 0.9$). For $|x| < 0.01$: $n > \log_{1/0.9}(250) \approx 52$.
> - Bottleneck is x-convergence, ~52 iterations. The "about 50" claim is correct.

This took 4 lines of arithmetic. It would have taken 0 lines to make a wrong claim. Always do the arithmetic.

### Plot/text consistency

If the plot shows trajectory ending near origin after 30 dots, but the prose says "converges in ~50 steps", one of them is wrong. Recompute.

If the prose says "diverges when $\alpha > 0.2$" and the slider's diverge-threshold visually is $\alpha \approx 0.21$, that's close enough. If the visual threshold is $\alpha \approx 0.5$, the prose is wrong (or the simulation is using a different formula).

---

## 4. Notation consistency

Within one page, one symbol = one meaning. This sounds obvious and is constantly violated.

### Common notation drifts

| Drift | Example | Fix |
|---|---|---|
| Symbol reused | $x$ for both input data and parameter | Rename one to $\theta$ or $w$ |
| Notation switched | $\nabla f$ in §2, $\text{grad}\, f$ in §4 | Pick one, use throughout |
| Subscript style switched | $x_t$ then $x^{(t)}$ | Pick one. $x_t$ for sequences, $x^{(t)}$ for indexed elements |
| Bold/italic inconsistency | $x$ as scalar in one place, vector in another | Use bold $\mathbf{x}$ for vectors, plain $x$ for scalars; commit |
| Function arguments dropped | $f(x)$ then $f$ then $f(\theta)$ | If $f$ varies, write its argument every time |
| Probability vs. expectation | $P[X]$ vs $E[X]$ — readers may confuse | Use $\Pr$ or $\mathbb{P}$ for probability, $\mathbb{E}$ for expectation |

### The notation table

If a page introduces more than 5 symbols, it benefits from a small table at the top:

> *Notation:* $\theta$ — parameters · $\alpha$ — learning rate · $\nabla$ — gradient · $\mathcal{L}$ — loss · $t$ — iteration index.

Once readers can refer back, they'll thank you.

---

## 5. Citation honesty

The temptation: drop a paper citation to look authoritative. The danger: if the citation is wrong, the entire page's credibility collapses.

### Rules

1. **Only cite what you can verify.** If you remember "Polyak proposed momentum in the 60s" but don't remember the exact paper, write "Polyak's heavy-ball method, 1960s" — don't fabricate "Polyak 1962". (The actual reference is Polyak 1964, *USSR Comput. Math. & Math. Phys.* 4(5).)

2. **Authors + year + venue, or nothing.** Half-citations like "Goodfellow et al." without a year are noise. Either give the full reference or describe the work without attribution.

3. **Recent results**: be conservative. The field moves; what was state-of-the-art in 2023 may be obsolete. Use phrases like "as of [year]" or "around the time of writing".

4. **For canonical textbook results**, cite the textbook chapter, not a paper. "See Boyd & Vandenberghe, *Convex Optimization*, §9.3" is more useful than digging up the original 1950s paper.

5. **Never cite from memory if the citation appears in the rendered output.** If you're unsure, search or remove.

### What to do when uncertain

> *Bad*: "As shown in Schmidhuber 2015, this technique..."
>
> *Better*: "This technique has been used in deep learning at least since the early 2010s."
>
> *Best (if true)*: "This technique is described in Section 6.5 of Goodfellow, Bengio, and Courville's *Deep Learning* (2016)."

Honesty about uncertainty beats false specificity every time.

---

## 6. Code verification

Every code snippet shown to the reader is a contract: this code, run as written, behaves as described.

### Pre-output trace

For any non-trivial snippet, manually trace at least one execution path:

```python
def gradient_descent(f_grad, x0, lr=0.01, steps=100):
    x = x0
    for _ in range(steps):
        x = x - lr * f_grad(x)
    return x
```

Trace: with `x0 = 1.0`, `f_grad = lambda x: 2*x` (so $f(x) = x^2$), `lr = 0.1`, `steps = 3`:
- iter 1: $x = 1 - 0.1 \cdot 2 = 0.8$
- iter 2: $x = 0.8 - 0.1 \cdot 1.6 = 0.64$
- iter 3: $x = 0.64 - 0.1 \cdot 1.28 = 0.512$
- return 0.512

If you want to claim "converges to 0", you'd need many more steps; with steps=3 and these settings, x is still 0.512. If the prose claims otherwise, fix it.

### API freshness

Library APIs drift. If you're showing code that uses a recent library (PyTorch, JAX, sklearn, swift charts, etc.), the version matters.

- **State the version** if your code uses any feature added in the last 2 years: `# Requires PyTorch 2.0+`.
- **Don't invent function signatures.** If unsure whether `torch.nn.functional.cross_entropy` takes `weight` as a kwarg, look it up — don't guess.
- **For cutting-edge APIs**, prefer the official docs link over reproducing the API exactly.

### Edge cases

For any function, explicitly think:
- What if input is empty / zero / one element?
- What if input is very large (overflow, memory)?
- What if input is `NaN` or `Infinity`?
- What if a parameter is at its boundary ($\beta = 0$, $\beta = 1$)?

Either handle the edge case in code, or state in prose what the limitation is.

---

## 7. The uncertainty admission

Sometimes you're not sure. The right response is to say so, not to confabulate confidence.

### Phrases that admit uncertainty honestly

- "I believe the canonical formulation is..."
- "The standard result is roughly..."
- "Without re-deriving, I'd estimate..."
- "I'd want to double-check this, but..."

### Phrases that fake confidence

- "Clearly,"
- "It is well known that"
- "Trivially,"
- "As everyone knows,"
- Citing a paper without specifying the paper

### When to flag uncertainty visibly to the reader

If a formula or fact is borderline, put it in a `.note` box rather than as a main claim. Example:

> **NOTE.** The condition $\alpha < 2/L$ is sufficient for convergence on convex quadratics. The general convex non-quadratic case requires the same bound but the proof is more involved; see Boyd & Vandenberghe §9.3.

The note shows you're aware of the boundary and pointed the reader to authoritative material.

---

## 8. The verification trail (chat-side, not artifact-side)

The user prefers that every reply ends with a one-line verification statement. This is **separate** from the artifact itself — it's metadata for the chat.

### Format

After the artifact is presented, add a final paragraph in the chat reply (not in the artifact) like:

> **Đã verify**: [list 2-4 specific things you re-checked]. Inconsistencies found: [list any caught + how fixed, or "none"]. Items I'm uncertain about: [list any, or "none"].

### Examples

> **Đã verify**: tính lại Hessian của $f = x^2 + 5y^2$ → diag(2, 10), max eigenvalue 10, nên $L = 10$ và ngưỡng $\alpha < 0.2$ đúng. Verify gradient $\nabla f = (2x, 10y)$ bằng đạo hàm riêng. Trace heavy-ball momentum với $\beta = 0$ — degenerate về SGD đúng. Inconsistencies: none.

> **Đã verify**: re-checked the convexity claim — quadratic with positive-definite Hessian is convex ✓. Verified the convergence-rate citation: Nesterov 1983 is the right reference for accelerated GD, not Polyak. **Caught and fixed**: had originally said "Polyak 1983 introduced acceleration" — corrected to "Nesterov 1983". Items I'm uncertain about: the exact constant in the accelerated rate; I stated $O(1/t^2)$ which is the standard form, but the leading constant depends on assumption — flagged in caveat.

The point is **not** ritual incantation ("I have verified this"). The point is showing the reader **what specifically was checked**, so they can trust the parts that were verified and double-check the parts that weren't.

If you didn't verify something, don't claim you did. If you can't verify something (e.g., cutting-edge claim, citation you don't recall precisely), say so.

---

## 9. The minimum viable verification

If time/space is tight, do at minimum:

1. **One numerical recompute** — pick the most-cited number on the page and verify it from scratch.
2. **One symbol consistency check** — pick the most-used Greek letter; is it used consistently?
3. **One limit case** — pick the most general formula; what does it degenerate to at boundary?
4. **One citation check** — pick the most authoritative-sounding citation; do you actually know it's right?

If any of these fails, do a deeper pass before sending.

### The "would I bet on this?" test

Final mental check before shipping. For each claim on the page, ask: *would I bet money it's correct?* "Yes" → ship. "Probably" → soften the claim or verify more. "I'd hope so" → don't ship; either verify or remove. Most epistemic failures come from "probably" claims being shipped as confident assertions; this test catches them.
