# MA207: Syntax Reference & Notes for Verbose Lean4

This document catalogs the English-like syntax available in the `verbose-lean4` library. It maps the verbose commands to their likely underlying Lean logic and purpose. This would be useful to look up in case you forget some syntax for `verbose-lean4` used in the course.

## 1. Top-Level Commands

These commands are used to state theorems, examples, or exercises.

| Verbose Syntax | Description |
| :--- | :--- |
| **Exercise** "Title"<br>**Given:** ...<br>**Assume:** ...<br>**Conclusion:** ...<br>**Proof:** ... **QED** | Defines an exercise with given objects, assumptions, and a conclusion. |
| **Example** "Title" ... | Same as `Exercise`. |
| **Lemma** name "Title" ... | Defines a named lemma. |
| **Exercise-lemma** name "Title" ... | Defines a named lemma which is an exercise to be proved. |

**Structure:**
```lean
Exercise "My Exercise"
  Given: (n : ℕ)
  Assume: (h : n > 0)
  Conclusion: n + 1 > 1
Proof:
  ...
QED
```

The **Lemma**'s(and **Exercise-lemma**'s) can be used later as facts without mentioning the name, it gets added to the database of proved theorems and is globally available for that file.

> If you face syntax highlighting issue with `Exercise-lemma` you can locally change it to `Lemma`, but make sure to change it back before submission.

## 2. Introducing & naming Objects (`Fix`, `Set`)

Used to introduce variables into the context (universal quantifiers) or define local values. When you want to simplify goal with `∀`, you would want to use `Fix`.

| Verbose Syntax | Underlying Idea | Example |
| :--- | :--- | :--- |
| **Fix** x | `intro x` | `Fix n` |
| **Fix** x : T | `intro (x : T)` | `Fix n : ℕ` |
| **Fix** x < y | `intro x (h : x < y)` | `Fix n < 10` |
| **Fix** x ∈ S | `intro x (h : x ∈ S)` | `Fix n ∈ A` |
| **Set** x := val | `let x := val` | `Set n := max a b` |

**Multiple introductions:** `Fix x y z` or `Fix n ≥ N`.

## 3. Assumptions (`Assume`)

Used to introduce hypotheses (implications) or start a proof by contradiction.

| Verbose Syntax | Underlying Idea | Example |
| :--- | :--- | :--- |
| **Assume** h | `intro h` | `Assume h` |
| **Assume** h : P | `intro (h : P)` | `Assume h : n > 0` |
| **Assume that** ... | Syntactic sugar for `Assume` | `Assume that n > 0` |
| **Assume for contradiction** h : P | `by_contra h` | `Assume for contradiction H : n ≠ n` |

## 4. Forward Reasoning (`By`, `Since`)

Used to deduce new facts from existing ones.

### `By ...` (Focus on application)

| Verbose Syntax | Lean Equivalent/Idea | Example |
| :--- | :--- | :--- |
| **By** h **we get** h₁ | `obtain` / `have` | `By h we get (h₁ : x > 0)` |
| **By** h **applied to** x **we get** ... | `specialize` + `obtain` | `By h applied to 0 we get h₀` |
| **By** h ... **using that** ... | `specialize` with proofs | `By h applied to ε using that ε > 0 ...` |
| **By** h **we choose** x **such that** ... | `choose` | `By h we choose n such that H : n > N` |
| **By** h **it suffices to prove** ... | `suffices` | `By h it suffices to prove x > 0` |

### `Since ...` (Focus on facts)

| Verbose Syntax | Lean Equivalent/Idea | Example |
| :--- | :--- | :--- |
| **Since** P **we get** ... | `have` / `obtain` | `Since x > 0 we get x ≠ 0` |
| **Since** P **and** Q **we get** ... | `have` (multiple facts) | `Since x > 0 and y > 0 we get x + y > 0` |
| **Since** P ∧ Q **we get** hP **and** hQ | `obtain ⟨hP, hQ⟩` | `Since P ∧ Q we get hP : P and hQ : Q` |
| **Since** P **we conclude that** Q | `have ...; exact ...` | `Since x = y we conclude that f x = f y` |
| **Since** P **we choose** x **such that** ... | `choose` | `Since ∀ y, ∃ x... we choose f...` |
| **Since** P **it suffices to prove that** ... | `suffices` | `Since P → Q it suffices to prove that P` |

> **Statement Rewriting:** If `P` is an equivalence (`A ⇔ B`), `Since P ...` can be used to replace occurrences of `A` with `B` (or vice versa) in the goal or conclusion.
> *   `Since A ⇔ B we conclude that ...` (concludes goal `B` if `A` is true, or allows proving `A` if goal is `B`).
> *   `Since A ⇔ B it suffices to prove that ...` (replaces `A` with `B` in the goal).

> **Note:** You can extract multiple facts: `Since ... we get h₁ : P and h₂ : Q`.

## 5. Goal Management (`Lets`, `We`)

Commands to structure the proof flow, split goals, or change the goal state.

### `Let's ...`

| Verbose Syntax | Description |
| :--- | :--- |
| **Let's prove that** P | Changes goal to `P` (useful for `show` or splitting `∨`). |
| **Let's prove that** x **works** | `use x` (for `∃`). |
| **Let's first prove that** P | Starts first subgoal (for `⇔` or `∧`). |
| **Let's now prove that** Q | Switches to next subgoal (for `⇔` or `∧`). |
| **Let's prove by induction** H : P | Defines induction goal P with name H |
| **Let's prove it's contradictory** | `exfalso` |
| **Let's prove the contrapositive:** P | `contrapose` (changes goal to P). |
| **It suffices to prove that** P | `suffices` |
| **It suffices to prove that** P **and** Q | `suffices` (splits goal) |

### `We ...` (Goal & Context Manipulation)

| Verbose Syntax | Idea in English | Lean Equivalent |
| :--- | :--- | :--- |
| **We proceed using** h | Case analysis on a hypothesis | `cases h` |
| **We proceed depending on** P | Case analysis on a proposition (Law of Excluded Middle) | `by_cases P` |
| **We discuss depending on whether** P **or** Q | Discuss cases (P or Q), both should be proved separately| `by_cases` / splitting logic |
| **We rewrite using** h | Rewrite the goal using an equality | `rw [h]` |
| **We rewrite using** h **at** h' | Rewrite a hypothesis using an equality | `rw [h] at h'` |
| **We unfold** f | Unfold a definition | `unfold f` |
| **We rename** x **to** y | Rename a variable | `rename_i` or structure renaming |
| **We forget** x | Clear a variable/hypothesis from context | `clear x` |
| **We reformulate** h **as** P | Change the type of a hypothesis (definitionally equal) | `change P at h` |
| **We push the negation** | Push negations inward | `push_neg` |
| **We contrapose** | Prove the contrapositive | `contrapose` |

## 6. Closing the Goal (`We conclude`)

| Verbose Syntax | Lean Equivalent/Idea |
| :--- | :--- |
| **We conclude by** h | `exact h` or `apply h` |
| **We conclude by** h **applied to** x | `exact h x` |
| **We combine** h **and** h' | `linarith`, `ring`, or custom combination logic. |
| **We compute** | `norm_num`, `ring`, `simp` (computational proof). |

## 7. Intermediate Facts (`Fact`, `Claim`)

Used to establish local lemmas within a proof.

```lean
Fact key : n + n = 2*n by
  ring
```
*   **Fact** name : statement **by** proof
*   **Fact** name : statement **from** justification
*   **Fact** name : statement **since** facts

## 8. Calculations (`Calc`)

Structured calculation blocks similar to `calc`.

```lean
Calc A = B by computation
  _    = C since ...
  _    < D from ...
```

*   **by computation**: Justified by `ring`, `norm_num`, etc. Handles standard algebraic rules, absolute values inequalities, and max/min properties.
*   **by hypothesis**: Justified by `assumption`.
*   **from** h: Justified by a specific fact.
*   **since** P: Justified by a statement (can use `and` for multiple: `since P and Q`).

**Placeholder `_`:** Use `_` as the LHS to repeat the RHS of the previous line (e.g., `_ = 3*a`).

> **Indentation Tip:** The safest way to format `Calc` is to leave `Calc` alone on its line and indent the following lines, keeping the same indentation level for all steps.

> **Strict Inequalities:** Linking an inequality with a strict inequality results in a strict inequality.
> *   `a ≤ b` and `b < c` implies `a < c`.

## 9. Assistance (`help`)

The `help` command provides suggestions based on the current context or a specific hypothesis.

| Verbose Syntax | Description |
| :--- | :--- |
| **help** | Provides suggestions for the current goal. |
| **help** h | Provides suggestions for using hypothesis `h`. |

## 10. Common Abbreviations & Typing

Some syntactic sugars and typing tips found in exercises:

| Abbreviation | Expands to | Example |
| :--- | :--- | :--- |
| `∀ ε > 0` | `∀ ε, ε > 0 ⇒ ...` | `Fix ε > 0` |
| `∃ n ≥ N` | `∃ n, n ≥ N ∧ ...` | `∃ n ≥ N, u n = 0` |
| `∀ x ∈ A` | `∀ x, x ∈ A ⇒ ...` | `∀ x ∈ [0, 1], ...` |
| `∀ x y` | `∀ x, ∀ y` | `Fix x y` |

**Typing Special Characters:**
*   **Conjunction (`∧`):** Type `\and`.
*   **Divides (`∣`):** Type `,|` or `,dvd`.
*   **Absolute Value (`|x|`):** Standard bar `|` but **no spaces** inside (e.g., `|x|`, not `| x |`).
*   **Common symbols:** `≠` (`\ne`), `≤` (`\le`), `≥` (`\ge`), `↦` (`\mapsto`), `∘` (`\comp`), `∨` (`\or`).

## 11. Common Predicates

A list of standard predicates used throughout the exercises.

| Predicate | Meaning |
| :--- | :--- |
| `n is even` / `n is odd` | Integer parity. |
| `f is even` / `f is odd` | Function parity. |
| `f is non-decreasing` | `∀ x y, x ≤ y ⇒ f x ≤ f y` |
| `f is non-increasing` | `∀ x y, x ≤ y ⇒ f x ≥ f y` |
| `f is surjective` | `∀ y, ∃ x, f x = y` |
| `f is injective` | `∀ x y, f x = f y ⇒ x = y` |
| `u tends to l` | Sequence limit: `∀ ε > 0, ∃ N, ∀ n ≥ N, |u n - l| ≤ ε` |
| `u tends to +∞` | `∀ A, ∃ N, ∀ n ≥ N, u n ≥ A` |
| `x bounds from above A` | `∀ a ∈ A, a ≤ x` |
| `x is the supremum of A` | `x` is the least upper bound of `A`. |
| `f is continuous at x₀` | Epsilon-delta continuity. |
| `f is sequentially continuous at x₀` | `∀ u, u tends to x₀ ⇒ f ∘ u tends to f x₀` |
| `u is Cauchy` | `∀ ε > 0, ∃ N, ∀ p q ≥ N, |u p - u q| ≤ ε` |
| `a is a cluster point of u` | `∃ φ, φ is an extraction ∧ (u ∘ φ) tends to a` |
| `φ is an extraction` | `φ : ℕ → ℕ` is strictly increasing (`∀ n m, n < m → φ n < φ m`). |

## 12. Set Notation

Syntax for working with sets of real numbers (`Set ℝ`), used in later exercises.

| Syntax | Meaning | Example |
| :--- | :--- | :--- |
| `x ∈ A` | Element membership | `Fix x ∈ A` |
| `x ∉ A` | Negation of membership | `Fact : x ∉ A` |
| `{x | P x}` | Set builder notation | `Set A := {x | x^2 < 2}` |
| `[a, b]` | Closed interval | `∀ x ∈ [0, 1], ...` |

## 13. Useful Implicit Lemmas

The software ("Computation" or "Since") implicitly knows these facts. You can use them without proof.

**Absolute Values:**
*   Triangle Inequality: `|x + y| ≤ |x| + |y|`
*   Reverse Triangle: `|x - y| ≤ |x - z| + |z - y|`
*   Symmetry: `|x - y| = |y - x|`
*   Bounds: `|x| ≤ y ⇔ -y ≤ x ∧ x ≤ y`
*   Positivity: `|x| ≥ 0`, `x ≠ 0 ⇒ |x| > 0`

**Max/Min:**
*   `p ≤ max p q` and `q ≤ max p q`
*   `r ≥ max p q ⇔ r ≥ p ∧ r ≥ q`

**Order & Limits:**
*   `x < y ∨ x = y ∨ x > y` (Trichotomy)
*   `u tends to l ∧ u tends to l' ⇒ l = l'` (Uniqueness)
*   `1/(n+1) > 0`
*   `(sequence n ↦ x) tends to x` (Constant sequence)

**Logic & Parity:**
*   `¬ (n is even) ⇔ n is odd`
*   `¬ (n is odd) ⇔ n is even`
*   `x ≤ y ∨ y ≤ x` (Total order)
*   `x ≤ y ∧ y ≤ x ⇒ x = y` (Anti-symmetry)


---
If you find anything extra you would like to add/any errors in this document, please inform the Professor or the TA for the same. Hope this is helpful!

---
*Course: MA207 Spring 2026, Mathematics Department, IISc Bengaluru* \
*Intructor: Professor Siddhartha Gadgil, Mathematics Department, IISc Bengaluru* \
*Notes made by: Anirudh Gupta, UG 3rd year BTech, IISc*

> Generated with help of Gemini