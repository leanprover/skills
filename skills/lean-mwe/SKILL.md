---
name: lean-mwe
description: Create minimal working examples (MWEs) from Lean errors for bug reports. Use when minimizing a Lean error, creating an MWE, or preparing a bug report for lean4 or mathlib4.
---

# Minimizing Lean Errors

## Workflow

1. **Set up the guard** (`#guard_msgs` or `#guard_panic`)
2. **Run `lake exe minimize`**
3. **Review and polish** the output

## Repository Setup

For Mathlib-related bugs:
```bash
cd /tmp
git clone https://github.com/kim-em/mathlib-minimizer.git
cd mathlib-minimizer
lake exe cache get
```

For pure Lean 4 bugs:
```bash
cd /tmp
git clone https://github.com/kim-em/lean-minimizer.git
cd lean-minimizer
```

## Step 1: Create the Test File

### For Regular Errors

Use `#guard_msgs` to capture the exact error:

```lean
import Mathlib.SomeModule

/--
error: the exact error message
goes here verbatim
-/
#guard_msgs in
example : ... := by some_tactic
```

### For Panics

```lean
import Mathlib.SomeModule

#guard_msgs in
#guard_panic in
some_command_that_panics
```

## Step 2: Verify the Guard Works

```bash
lake env lean YourFile.lean
```

No output = success (guard passed). Error output = guard failed, and you need to redesign the test case.

## Step 3: Run the Minimizer

```bash
lake exe minimize YourFile.lean
```

Output is written to `YourFile.out.lean`.

### Useful Options

- `--resume`: Continue from the output file if interrupted
- `--quiet`: Suppress progress output
- `--only-delete`: Only run the deletion pass
- `--only-import-inlining`: Only inline imports

**Never use `--no-import-inlining`**. The entire point is to produce a self-contained file.

### For Long-Running Minimizations

Use `--resume` to continue from where you left off:

```bash
# Initial run (Ctrl-C if needed)
lake exe minimize YourFile.lean

# Resume later
lake exe minimize YourFile.lean --resume
```

After manually editing the `.out.lean` file, always use `--resume` to continue from the edited state.

## Step 4: Review the Output

```bash
lake env lean YourFile.out.lean
```

### Checklist Before Filing

- [ ] The `.out.lean` file compiles with the expected error/panic
- [ ] No Mathlib imports remain (ideal) or minimal imports remain
- [ ] The error message in `#guard_msgs` matches exactly
