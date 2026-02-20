# Lean 4 Development Guide

This file provides core guidance for AI coding agents working with
[Lean 4](https://github.com/leanprover/lean4) and
[Mathlib](https://github.com/leanprover-community/mathlib4).

For more detailed guidance, see the individual skills in `skills/`.

## Proof Methodology

1. **One step at a time.** Write exactly one tactic, then check diagnostics
   before continuing. Use `done` to see the exact unsolved goals.
2. **Error priority.** Fix errors in this order: syntax → type → unsolved
   goals → linter warnings. A single syntax error can cascade into dozens of
   spurious messages.
3. **Hardest case first.** When a proof has multiple cases, `sorry` the easy
   ones and work on the hardest case first. If the hard case fails, all effort
   on easy cases is wasted.
4. **`sorry` is fine for placeholders.** Lean treats `sorry` as an axiom. You
   can work on theorem A even if helper lemma B still has a `sorry`.
5. **Stop at first error.** When any error appears after a tactic, stop, fix
   the error, then continue. Don't write more tactics on top of errors.
6. **Clean up after proving.** Combine redundant steps, test if `simp` can
   replace earlier steps, find the minimal proof.

## Building

### Lean 4

```bash
make -j -C build/release            # Build
make -j -C build/release test ARGS="-j$(nproc)"  # Test suite
cd tests/lean/run && ./test_single.sh example.lean  # Single test
```

### Mathlib

```bash
lake exe cache get                   # Fetch oleans cache first
lake build -q --log-level=info       # Build quietly
lake build Mathlib.Foo.Bar -q --log-level=info  # Build one module
```

## PR Conventions

Commit format: `<type>: <subject>` where type is one of: `feat`, `fix`, `doc`,
`style`, `refactor`, `test`, `chore`, `perf`. Subject uses imperative present
tense, no capitalization, no trailing period.

For `feat`/`fix` PRs, the body must start with "This PR " and needs a
`changelog-*` label.

## Writing Tests

- All new tests go in `tests/lean/run/`
- Use `#guard_msgs` to check for specific messages
- Tests run on a success/failure basis (no expected output files)

## Dependent Type Rewriting

When `rw` fails with "motive is not type correct" because the term appears in
dependent types, use the generalize-then-instantiate pattern:

```lean
suffices ∀ s, statement_about s by
  convert this ?_ <;> exact the_equality
intro s
-- prove for arbitrary s
```
