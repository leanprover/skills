---
name: mathlib-build
description: Building Mathlib
---

# Building Mathlib

Fetch the Mathlib olean cache before build:

```bash
lake exe cache get
```

Use `lake exe cache get!` (with `!`) to force re-download if the cache appears corrupt.

When building Mathlib reduce verbosity to save on tokens:

```bash
lake build -q --log-level=info
```

For merge conflict resolution or small fixes build only the affected files: `lake build Mathlib.Foo.Bar -q --log-level=info`.
Often it is fine to leave a complete build to CI. If you need a thorough local build, use `lake build Mathlib MathlibTest Archive Counterexamples && lake exe runLinter`.

