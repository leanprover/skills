---
name: lean-proof
description: Use when asked to prove something in Lean. Covers one-step-at-a-time proving, error priority, working on the hardest case first, proof cleanup, and handling dependent type rewriting issues.
---

# Lean Proof Methodology

These are non-negotiable constraints for writing Lean proofs correctly.

## One Step at a Time

Write one tactic, check diagnostics (use `done` to see unsolved goals), repeat. Never write multiple tactics before checking.

**`by sorry` is acceptable**: For placeholders you're not actively working on.
**`done` is required**: When you expect there to be next steps in an active proof.

## Error Priority

Fix errors in this order — higher-priority errors make lower-priority ones unreliable:

1. **Syntax errors** → 2. **Type errors** → 3. **Unsolved goals / tactic failures** → 4. **Linter warnings**

"Unsolved goals" errors appear on `by` or `=>` lines, NOT where you add tactics. If there's an "unsolved goals" on line 59 but a tactic error on line 65 — fix line 65 FIRST.

Stop writing tactics after any error.

## Work on the Hardest Case First

### Across Theorems

Go directly to the target theorem. Don't fill in `sorry`s in helper lemmas first — Lean treats `sorry` as an axiom, so dependent theorems still work.

Move sorries earlier in the file by replacing a `sorry` proof with references to simpler lemmas:

```lean
-- Before:
theorem main_theorem : A = C := by sorry

-- After:
theorem lemma1 : A = B := by sorry
theorem lemma2 : B = C := by sorry
theorem main_theorem : A = C := by
  rw [lemma1, lemma2]
```

### Within a Proof

When a proof has multiple cases, `sorry` the easy cases and work on the hardest one first. If the hard case fails, effort on easy cases is wasted.

```lean
match n with
| 0 => sorry -- fill in later
| 1 => sorry -- fill in later
| n + 2 => -- WORK ON THIS FIRST
```

## Proof Cleanup

After getting a proof to work, clean it up immediately:
- Combine redundant steps (`rw [a]; rw [b]` → `rw [a, b]`)
- Test if `simp` can handle more (remove earlier steps one by one)
- Find the truly minimal proof

## Dependent Type Rewriting Issues

**When you encounter "motive is not type correct" or similar errors during rewriting:**

### The Problem

Rewriting a term `b` that appears in dependent types (like `hab : a ≤ b`) fails because the motive cannot abstract over the dependencies.

```lean
have hb : b = f x
rw [hb]  -- Error: motive is not type correct
```

### The Solution: Generalize First, Instantiate Last

Prove a generalized statement for an arbitrary parameter, then instantiate:

```lean
suffices ∀ s, statement_about s by
  have h_specific := the_equality_you_have
  convert this ?_ <;> exact h_specific
intro s
-- Now prove the general statement for arbitrary s
```

This works because the generalized statement has no dependencies on the problematic term, and `convert` handles the dependent type coercions at the end.

## Verification

Never declare a proof complete while `sorry` placeholders or error diagnostics remain.
