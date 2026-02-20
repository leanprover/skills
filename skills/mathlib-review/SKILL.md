---
name: mathlib-review
description: Review guidelines for Mathlib PRs. Use when reviewing pull requests, checking code quality, or assessing whether a PR is ready to merge.
---

# Mathlib PR Review

## Attributes and API

- New definitions should come with associated lemmas and appropriate attributes (`@[simp]`, `@[ext]`, etc.).
- Watch for instance diamonds.
- Prefer bundled morphisms, `FunLike` API for morphism classes, `SetLike` API for subobject classes.

## Style Points Specific to Mathlib

- **Simp squeezing:** Terminal `simp` calls should NOT be squeezed (replaced with `simp only [...]`) unless there's a measured performance problem. Unsqueezed `simp` is more maintainable and doesn't break when lemmas are renamed.
- **Normal forms:** Prefer `s.Nonempty` over alternatives. Use `hne : x ≠ ⊥` in hypotheses (easier to check), `hlt : ⊥ < x` in conclusions (more powerful).
- **Transparency:** Needing `erw`, or `rfl` after `simp`/`rw` usually means the API is missing lemmas.
- **File size:** Consider splitting files that exceed ~1000 lines or cover multiple topics.

## Reference Guides

The full review guide and style references:

- **Review guide:** https://leanprover-community.github.io/contribute/pr-review.html
- **Naming conventions:** https://leanprover-community.github.io/contribute/naming.html
- **Code style:** https://leanprover-community.github.io/contribute/style.html
- **Documentation style:** https://leanprover-community.github.io/contribute/doc.html
