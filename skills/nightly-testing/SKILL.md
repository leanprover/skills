---
name: nightly-testing
description: Understanding the Lean/Mathlib nightly testing infrastructure. Use when working on toolchain bumps, adaptation PRs, or investigating nightly CI failures.
---

# Nightly Testing

Lean 4 publishes nightly toolchain builds from `master`. Batteries and Mathlib each have a `nightly-testing` branch that tracks these nightlies and runs CI against them. When CI passes, a `nightly-testing-YYYY-MM-DD` tag is created. When it fails, fixes are needed before the new toolchain can be adopted.

## The mathlib4-nightly-testing Fork

Mathlib's nightly testing lives in a **separate fork**: `leanprover-community/mathlib4-nightly-testing`, not on the main `leanprover-community/mathlib4` repo. This keeps experimental toolchain branches out of the main repository. When Lean PRs affect Mathlib, `lean-pr-testing-NNNN` branches are created here automatically.

## Key Branches

- **`nightly-testing`** — tracks the latest Lean nightly. CI runs here determine whether a nightly is usable.
- **`nightly-with-mathlib`** — points to the latest nightly that passes Mathlib CI. Lean PRs that may affect downstream should base off this branch.
- **`bump/v4.X.Y`** — accumulates reviewed adaptations for an upcoming Lean release. Adaptation PRs merge `nightly-testing` changes into this branch, and `master` is regularly merged in during release cycles.
- **`lean-pr-testing-NNNN`** — created automatically to test specific Lean PRs against Mathlib.

## Zulip

https://leanprover.zulipchat.com/#narrow/channel/nightly-testing has up-to-date status on nightly builds, failures, and adaptation work.

## Full Details

The canonical reference for branch and tag conventions across lean4, Batteries, and Mathlib is:
https://leanprover-community.github.io/contribute/tags_and_branches.html. If you are asked to work on nightly-testing, or lean-pr-testing-NNNN branches, you should read that page.
