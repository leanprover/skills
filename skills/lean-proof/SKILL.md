---
name: lean-proof
description: Methodology for writing Lean 4 proofs correctly. Covers one-step-at-a-time proving, error priority, working on the hardest case first, proof cleanup, and handling dependent type rewriting issues.
---

# Lean Proof Methodology

These are non-negotiable constraints for writing Lean proofs correctly.

## One Step at a Time

1. **ONE STEP AT A TIME**: Write exactly one tactic or proof step, then check diagnostics before continuing
2. **USE `done` TO CHECK GOALS**: When actively working on a proof, write `done` as your next step to see the exact unsolved goals via error diagnostics
3. **ALWAYS READ DIAGNOSTICS**: After each line, you MUST read the error messages and unsolved goals before proceeding
4. **NO MULTI-LINE PROOFS**: Never write multiple tactics in one `by` block when actively proving
5. **NO ASSUMPTIONS**: Do not assume what the next goal will be — always check what Lean actually shows you

**`by sorry` is acceptable**: For placeholders you're not actively working on.
**`done` is required**: When you expect there to be next steps in an active proof.

If you catch yourself writing multiple steps: immediately slow down, use `done` to check the actual goal state, and proceed one step at a time.

## Error Priority

**Always fix errors in this exact order:**

1. **Syntax errors FIRST** — These prevent the file from parsing at all
2. **Type errors** — These prevent the code from type-checking
3. **Unsolved goals / tactic failures** — These are proof-specific issues
4. **Linter warnings** — These are style issues only

**Never attempt to fix unsolved goals or tactic errors while syntax errors exist.** A single syntax error can cascade into dozens of spurious error messages.

### Understanding "Unsolved Goals" Errors

"Unsolved goals" appear on `by` or `=>` lines, NOT where you add tactics.

**If "unsolved goals" on line 59 but tactic error on line 65:** Fix line 65 FIRST. After tactic errors are fixed, use `done` to see remaining goals.

### Stop at First Error

When ANY error appears after a tactic:
1. STOP — no more tactics or cases
2. FIX THE ERROR first
3. Error means Lean's state isn't what you expect

Never continue writing proof steps after errors.

## Work on the Hardest Case First

### Across Theorems

When working on a specific theorem, **go directly to that theorem**. Don't get distracted filling in `sorry`s in helper lemmas first.

**Key insight**: Lean treats `sorry` as an axiom. If theorem A uses lemma B, and lemma B has a `sorry`, theorem A can still be worked on — Lean will accept lemma B as given. The `sorry`s in helper lemmas can be filled in later.

In successive steps, we want to move sorries earlier in the file, by replace the `sorry` proof of a theorem with the main steps, and then references to simpler lemmas which we insert before the theorem.

For example, replacing
```
theorem main_theorem : A = C := by sorry
```
with
```
theorem lemma1 : A = B := by sorry
theorem lemma2 : B = C := by sorry
theorem main_theorem : A = C := by
  rw [lemma1, lemma2]
```
is progress! (Assuming this actually reflects the structure of the proof.)

### Within a Proof

When a proof has multiple cases (e.g., `match` on a nat giving cases 0, 1, n+2), ALWAYS work on the hardest case first.

**Rationale**: If the hard case fails, all effort on easy cases is WASTED. Easy cases can always be filled in later with `sorry` or trivial proofs. The hard case determines whether the overall approach is viable.

```lean
-- RIGHT: sorry the easy cases, focus on the hard one
match n with
| 0 => sorry -- fill in later
| 1 => sorry -- fill in later
| n + 2 => -- WORK ON THIS FIRST
```

## Proof Cleanup

After getting a proof to work, clean it up immediately:
- Combine redundant steps (`rw [a]; rw [b]` -> `rw [a, b]`)
- Test if `simp` can handle more (remove earlier steps one by one)
- Find the truly minimal proof

Complete ALL cleanup steps before moving to the next proof.

## Dependent Type Rewriting Issues

**When you encounter "motive is not type correct" or similar dependent type errors during rewriting:**

### The Problem

When trying to rewrite a term `b` that appears in dependent types (like proofs `hab : a ≤ b`), direct rewriting with `rw` often fails because the motive cannot abstract over the dependencies.

```lean
-- Trying to rewrite b when hab depends on b
have hb : b = f x
rw [hb]  -- Error: motive is not type correct
```

### The Solution: Generalize First, Instantiate Last

Instead of rewriting the specific term, **prove a generalized statement for an arbitrary parameter, then instantiate it**:

```lean
suffices ∀ s, statement_about s by
  have h_specific := the_equality_you_have
  convert this ?_ <;> exact h_specific
intro s
-- Now prove the general statement for arbitrary s
```

**Why this works:**
1. The generalized statement has no dependencies on the specific problematic term
2. You prove it for an arbitrary parameter, avoiding dependent type issues
3. `convert` at the end handles all the messy dependent type coercions

## Verification

**Never declare code "complete" until verified:**
- Zero error diagnostics
- No `sorry` placeholders
- Code actually works

After every change: wait for diagnostics, READ actual errors, never assume fixes worked.
