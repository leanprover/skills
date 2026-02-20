---
name: lean-mwe
description: Create minimal working examples (MWEs) from Lean errors for bug reports. Use when minimizing a Lean error, creating an MWE, or preparing a bug report for lean4 or mathlib4.
---

# Minimizing Lean Errors

This skill covers how to create minimal reproducible examples for bug reports, using the **lean-minimizer** tool for automated delta debugging.

## Overview

The workflow is:
1. **Set up the guard** (`#guard_msgs` or `#guard_panic`)
2. **Run `lake exe minimize`** and let it do the work
3. **Review and polish** the output if needed

## Repository Setup

For Mathlib-related bugs, use a project that has both mathlib and lean-minimizer as dependencies:

```bash
cd /tmp
git clone https://github.com/kim-em/mathlib-minimizer.git
cd mathlib-minimizer
lake exe cache get  # Get mathlib cache
```

For pure Lean 4 bugs, clone lean-minimizer directly:
```bash
cd /tmp
git clone https://github.com/kim-em/lean-minimizer.git
cd lean-minimizer
```

## Step 1: Create the Test File

Create a file with:
1. The necessary imports
2. The code that triggers the bug
3. A guard to verify the bug reproduces

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

Use `#guard_panic` to catch panics:

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

- **No output** = success (guard passed, error/panic reproduced as expected)
- **Error output** = guard failed (the bug didn't reproduce, or reproduced differently)

## Step 3: Run the Minimizer

```bash
lake exe minimize YourFile.lean
```

This will:
1. Parse the file and identify the invariant section (the `#guard_msgs` block)
2. Use delta debugging to remove unnecessary commands
3. Replace proof bodies with `sorry`
4. Simplify types and remove attributes
5. Minimize imports
6. Inline imports to create a self-contained file

Output is written to `YourFile.out.lean`.

### Useful Options

- `--resume`: Continue from the output file if interrupted
- `--quiet`: Suppress progress output
- `--only-delete`: Only run the deletion pass
- `--only-import-inlining`: Only inline imports

**Never use `--no-import-inlining`**. The entire point of minimization is to produce a self-contained file with no Mathlib imports.

### For Long-Running Minimizations

The minimizer can take a while for large files. **Use `--resume` to continue from where you left off**:

```bash
# Initial run (Ctrl-C if needed)
lake exe minimize YourFile.lean

# Resume later
lake exe minimize YourFile.lean --resume
```

After manually editing the `.out.lean` file, always use `--resume` to continue from the edited state rather than starting over.

## Step 4: Review the Output

```bash
lake env lean YourFile.out.lean
```

The minimizer should have:
- Removed all unnecessary declarations
- Replaced proof bodies with `sorry`
- Simplified types where possible
- Inlined all imports

## What the Minimizer Does (Passes)

1. **Module System Removal**: Removes `module` keyword and import modifiers
2. **Command Deletion**: Uses delta debugging to find minimal command set
3. **Empty Scope Removal**: Removes empty `section`/`namespace` blocks
4. **Singleton Namespace Flattening**: `namespace Foo def bar end Foo` -> `def Foo.bar`
5. **Body Replacement**: Replaces proofs with `sorry`
6. **Text Substitution**: Removes attributes, simplifies names
7. **Extends Simplification**: Simplifies structure inheritance
8. **Import Minimization**: Removes/replaces unnecessary imports
9. **Import Inlining**: Inlines imports to create self-contained file

## Final Checklist

Before filing the bug report:
- [ ] The `.out.lean` file compiles with the expected error/panic
- [ ] No Mathlib imports remain (ideal) or minimal imports remain
- [ ] The code is as simple as possible
- [ ] The error message in `#guard_msgs` matches exactly
