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

Used to introduce variables into the context (universal quantifiers) or define local values.

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
| **Since** P **we conclude that** Q | `have ...; exact ...` | `Since x = y we conclude that f x = f y` |
| **Since** P **we choose** x **such that** ... | `choose` | `Since ∀ y, ∃ x... we choose f...` |
| **Since** P **it suffices to prove that** ... | `suffices` | `Since P → Q it suffices to prove that P` |

## 5. Goal Management (`Lets`, `We`)

Commands to structure the proof flow, split goals, or change the goal state.

### `Let's ...`

| Verbose Syntax | Description |
| :--- | :--- |
| **Let's prove that** P | Changes goal to `P` (useful for `show` or splitting `∨`). |
| **Let's prove that** x **works** | `use x` (for `∃`). |
| **Let's first prove that** P | Starts the first subgoal of a split. |
| **Let's now prove that** Q | Switches to the next subgoal. |
| **Let's prove by induction** H : P | Defines induction goal P with name H |
| **Let's prove it's contradictory** | `exfalso` |
| **Let's prove the contrapositive:** P | `contrapose` (changes goal to P). |

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
  _    ≤ D from ...
```

*   **by computation**: Justified by `ring`, `norm_num`, etc.
*   **by hypothesis**: Justified by `assumption`.
*   **from** h: Justified by a specific fact.
*   **since** P: Justified by a statement.

## 9. Assistance (`help`)

The `help` command provides suggestions based on the current context or a specific hypothesis.

| Verbose Syntax | Description |
| :--- | :--- |
| **help** | Provides suggestions for the current goal. |
| **help** h | Provides suggestions for using hypothesis `h`. |

## Summary of "Connector" Syntax

*   **applied to** ... **and** ... : Passing arguments to functions/lemmas.
*   **using that** ... : Providing proofs for side-conditions (e.g., `x > 0`).
*   **such that** ... : Naming components of an existential or `and` (`obtain`).
*   **we get** ... : Bringing results into context.

---
If you find anything extra you would like to add/any errors in this document, please inform the Professor or the TA for the same. Hope this is helpful!

---
*Course: MA207 Spring 2026, Mathematics Department, IISc Bengaluru* \
*Intructor: Professor Siddhartha Gadgil, Mathematics Department, IISc Bengaluru* \
*Notes made by: Anirudh Gupta, UG 3rd year BTech, IISc*

> Generated with help of Gemini