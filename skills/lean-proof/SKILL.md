---
name: lean-proof
description: Use when asked to prove something in Lean 4 or Mathlib, fill in a `sorry`, or fix a failing Lean proof ("prove this in Lean", "fill the sorry", "this tactic proof doesn't work"). Covers checking diagnostics without an editor, one-step-at-a-time proving, error priority, working on the hardest case first, finding lemmas, proof cleanup, "motive is not type correct" rewrites, and verifying a proof is really done.
---

# Lean Proof Methodology

These are non-negotiable constraints for writing Lean proofs correctly.

## Checking Diagnostics

Every rule below depends on seeing Lean's errors and goals after each step.

- **Lean LSP MCP server connected** (e.g. lean-lsp-mcp): use its diagnostics and goal-state tools after every edit.
- **Otherwise**, from the project root (the directory with `lakefile.lean` or `lakefile.toml`), run:
  ```bash
  lake env lean path/to/File.lean
  ```
  This prints `File.lean:LINE:COL: error: ...`; no output means the file is clean. Imported modules must already be built (`lake build`; for Mathlib projects, `lake exe cache get` first). Each run re-elaborates the whole file, so do the work in a small scratch file that imports the module containing the target.
- To see the goal at a point without an editor, put `done` (or `trace_state`) there. The error message lists the remaining goals.

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

## Finding Lemmas and Closing Goals

- **Search:** `exact?`, `apply?`, `rw?`, `simp?` suggest a step; paste the suggestion in place of the call. `#check @name` shows a lemma's exact signature. For Mathlib names, Loogle (https://loogle.lean-lang.org) and LeanSearch take a type pattern or plain English.
- **Closers to try first:** `rfl`, `decide`, `omega` (linear `Nat`/`Int`), `simp`, `grind` (recent toolchains). With Mathlib, also `norm_num`, `ring`, `linarith`, `nlinarith`, `positivity`, `field_simp`, `aesop`, `tauto`.
- Guessed lemma names are usually wrong. Confirm a name with `#check` before building on it.

## "Motive is not type correct"

`rw [hb]` with `hb : b = t` fails when something else in the goal *depends* on `b`, such as a proof `hab : a ≤ b` passed as an argument (`f b hab`). Rewriting `b` alone would leave `hab` with the wrong type. Fixes, in order of preference:

1. **`subst hb`** (or `obtain rfl := hb`) when `b` is a local variable. This replaces `b` everywhere, including in `hab`.
2. **Revert the dependents, rewrite, reintroduce.** The motive then abstracts over `hab` too:
   ```lean
   revert hab
   rw [hb]
   intro hab
   ```
3. **`simp only [hb]`** or **`convert h using n`** when the dependent argument is a proof. Proof irrelevance lets these through where `rw` fails.
4. **Generalize first, specialize last.** State and prove the result for an arbitrary `s` (`suffices ∀ s, P s by exact this t`), which has no dependency on the problematic term.

## Verification

Never declare a proof complete while `sorry` placeholders or error diagnostics remain.

Confirm with:

```lean
#print axioms my_theorem
```

The output must not include `sorryAx`. A normal proof uses at most `propext`, `Classical.choice` and `Quot.sound`. `Lean.ofReduceBool` means `native_decide` was used, which trusts the compiler; say so when reporting.

Never make a proof go through by changing the theorem statement, adding an `axiom`, or leaving `sorry`/`admit`. If it is stuck, report where it is stuck and why.
