---
name: mathlib-pr
description: PR conventions for leanprover-community/mathlib4. Use when creating pull requests, writing commit messages, or managing labels for Mathlib contributions.
---

# Mathlib PR Conventions

## Commit Message Format

PR titles follow `<type>(<scope>): <subject>`.

**Types:** `feat`, `fix`, `doc`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`

**Scope** is the module path with the `Mathlib/` prefix stripped — e.g. `Data/Nat/Basic`, `Topology/Constructions`.

**Subject** uses imperative present tense, no capitalized first letter, no trailing period.

Full conventions: https://leanprover-community.github.io/contribute/commit.html

## Workflow

- PRs must come from **forks**, not branches on the main repo.
- Run `lake exe mk_all` when adding or removing files (updates the import root).
- PR dependencies use checkbox syntax in the description: `- [ ] depends on: #XXXX`
- Comment `!bench` on a PR to trigger performance benchmarking.

## Labels

Labels are added/removed via GitHub comments.

**Author-managed:**
- `awaiting-author` — reviewer feedback needs addressing
- `WIP` — work in progress
- `easy` — trivial PRs (single lemma, typo fix, <25 line diff)
- `help-wanted`, `please-adopt` — requesting help

**Topic:** `t-topology`, `t-algebra`, `t-combinatorics`, etc.

**Downstream projects:** `carleson`, `FLT`, etc.

**Automated:** `merge-conflict` is added/removed automatically when conflicts are detected or resolved.

## Merge Process

1. Reviewer approves and adds `maintainer-merge`
2. Maintainer adds `ready-to-merge`
3. Bors bot merges the PR

For **delegated** PRs (maintainer trusts author to finalize): the author comments `bors merge` to trigger the merge.

The review queue is at https://leanprover-community.github.io/queueboard/ — PRs with merge conflicts or pending CI don't appear there.

## Style and Naming

Before submitting, read the relevant guides — these are the authoritative references:

- **Naming conventions:** https://leanprover-community.github.io/contribute/naming.html
- **Code style:** https://leanprover-community.github.io/contribute/style.html
- **Documentation style:** https://leanprover-community.github.io/contribute/doc.html
- **PR lifecycle:** https://leanprover-community.github.io/contribute/index.html
