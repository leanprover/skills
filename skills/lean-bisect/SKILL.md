---
name: lean-bisect
description: Bisect Lean toolchain versions to find where behavior changes. Use when trying to identify which Lean 4 commit caused a regression or behavior change.
---

# Bisecting Lean Toolchains

This skill covers using the `lean-bisect` script (found in the lean4 repository at `script/lean-bisect`) to identify which Lean 4 commit introduced a behavior change or regression.

## Requirements for Test Files

**Test files must be self-contained with no Mathlib dependencies.**

lean-bisect works by testing your file against different Lean 4 toolchains. Since Mathlib is pinned to specific toolchains, Mathlib imports will fail on most versions being tested.

The test file should only use:
- Lean 4 standard library (`Lean.*`, `Std.*`)
- Axioms and local definitions
- No external package imports

## Usage Patterns

### Basic usage (auto-find regression)
```bash
script/lean-bisect /tmp/test.lean
```

### Bisect to a nightly
```bash
script/lean-bisect /tmp/test.lean ..nightly-2024-06-01
```

### Bisect between nightlies
```bash
script/lean-bisect /tmp/test.lean nightly-2024-01-01..nightly-2024-06-01
```

### Bisect between commits
```bash
script/lean-bisect /tmp/test.lean abc1234..def5678
```

### With timeout
```bash
script/lean-bisect /tmp/test.lean --timeout 30
```

## How It Determines Pass/Fail

The script uses a "signature" based on:
1. Exit code (0 = success, non-zero = failure, -124 = timeout)
2. stdout content
3. stderr content

By default, it looks for changes in this full signature. Use `--ignore-messages` to only consider exit code.

## Creating Test Files

### For Behavior Changes

```lean
/-
Test case: describe what changed

Expected:
- v4.XX.X: behavior A
- v4.YY.Y: behavior B
-/

-- Define minimal setup using axioms
axiom G : Type
axiom op : G -> G -> G

-- The test that shows the behavior difference
example : ... := by
  <the failing tactic call>
```

### For Error Messages

Use `#guard_msgs` to check specific error messages:
```lean
/--
error: the specific error that should appear
-/
#guard_msgs in
example : ... := by ...
```

## Options

- `--timeout N`: Timeout in seconds for each test
- `--ignore-messages`: Only compare exit codes, ignore stdout/stderr
- `--nightly-only`: When bisecting commits, only test nightly releases
- `--selftest`: Run self-test to verify the script works
- `--clear-cache`: Clear the CI artifact cache

## Caching

The script caches downloaded Lean builds in `~/.cache/lean_build_artifact/`. This speeds up repeated bisections significantly. Use `--clear-cache` if you need fresh builds.

## Workflow for Mathlib Issues

When the issue requires Mathlib:

1. **Create a minimal test case** that reproduces the issue
2. **Use lean-minimizer** to create a Mathlib-free version (see the `lean-mwe` skill)
3. **Run lean-bisect** on the minimized file
4. **Document the findings** including the problematic commit

## Tips

1. **Start with a known-good and known-bad version**: Before bisecting, verify your test file actually shows different behavior on the endpoints.
2. **Make the test deterministic**: Avoid tests that might have different behavior based on timing or randomness.
3. **Keep the test fast**: Each bisection step runs the full test. A 1-second test is much better than a 10-second test.
4. **For complex issues**: The issue might span multiple commits. Be prepared to bisect multiple times with different test cases.
