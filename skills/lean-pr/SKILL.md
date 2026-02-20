---
name: lean-pr
description: PR conventions for the leanprover/lean4 repository. Use when creating pull requests, writing commit messages, or following project conventions for Lean contributions.
---

# Lean PR Conventions

## Commit Message Format

All PR titles must follow the format:

```
<type>: <subject>
```

**`<type>`** is one of:
- `feat` — feature
- `fix` — bug fix
- `doc` — documentation
- `style` — formatting
- `refactor`
- `test` — adding missing tests
- `chore` — maintenance
- `perf` — performance improvement

**`<subject>`**: imperative present tense, lowercase, no period.

For `feat`/`fix` PRs, begin the description with "This PR " — the first paragraph is automatically used in release notes.

## Changelog Labels

Every `feat` or `fix` PR must have a `changelog-*` label:

| Label | Category |
|-------|----------|
| `changelog-language` | Language features and metaprograms |
| `changelog-tactics` | User-facing tactics |
| `changelog-server` | Language server, widgets, and IDE extensions |
| `changelog-pp` | Pretty printing |
| `changelog-library` | Library |
| `changelog-compiler` | Compiler, runtime, and FFI |
| `changelog-lake` | Lake |
| `changelog-doc` | Documentation |
| `changelog-ffi` | FFI changes |
| `changelog-other` | Other changes |
| `changelog-no` | Do not include in changelog |

## Module System for `src/` Files

Files in `src/Lean/`, `src/Std/`, and `src/lake/Lake/` must have both `module` and `prelude` declarations. With `prelude`, nothing is auto-imported — you must explicitly import `Init.*` modules.

```lean
module

prelude
import Init.While
import Init.Data.String.TakeDrop
public import Lean.Compiler.NameMangling
```

Check existing files in the same directory for the pattern.

Files outside these directories (e.g. `tests/`, `script/`) use just `module`.

## Copyright Headers

New files in `src/` require a copyright header:

```
/-
Copyright (c) YYYY Author or Organization. All rights reserved.
Released under Apache 2.0 license as described in the file LICENSE.
Authors: Author Name
-/
```

Check other recent files in the repository to determine the correct copyright holder. Test files (in `tests/`) do not need copyright headers.

## PR Conventions

Keep descriptions **concise**:

- Start with a paragraph beginning "This PR ..." — no section headers
- No "## Summary" header — just start with the text
- No "Test plan" section — we rely on CI
- No "Implementation details" section — the code speaks for itself
