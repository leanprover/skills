---
name: mathlib-build
description: Build Mathlib with appropriate verbosity settings. Use when building Mathlib or large Lean projects to reduce output noise while preserving diagnostics.
---

# Building Mathlib

When building Mathlib with `lake build`, use the following flags to reduce output verbosity:

```bash
lake build -q --log-level=info
```

## What This Does

- `-q` (or `--quiet`): Hides the progress indicator and "Building X" messages for each file
- `--log-level=info`: Ensures that `logInfo`, `logWarning`, and error messages still appear

## Why This Matters

A full Mathlib build touches thousands of files. Without `-q`, lake outputs a line for every file being built, which:
1. Produces enormous output
2. Makes it harder to spot actual errors and warnings
3. Provides no useful information for debugging

## Examples

```bash
# Build all of Mathlib quietly
lake build -q --log-level=info

# Build a specific module quietly
lake build Mathlib.Analysis.Calculus.Deriv.Basic -q --log-level=info

# Build multiple targets quietly
lake build Mathlib.Tactic Mathlib.Data.List.Basic -q --log-level=info
```

## Output Behavior

| Flag Combination | Progress Messages | Info | Warnings | Errors |
|------------------|-------------------|------|----------|--------|
| (none)           | Yes               | Yes  | Yes      | Yes    |
| `-q`             | No                | No   | Yes      | Yes    |
| `-q --log-level=info` | No           | Yes  | Yes      | Yes    |
| `--log-level=warning` | Yes          | No   | Yes      | Yes    |
| `--log-level=error` | Yes            | No   | No       | Yes    |

## Build Strategy

For merge conflict resolution or small fixes:
- Build only the affected files first: `lake build Mathlib.Foo.Bar -q --log-level=info`
- Don't wait for a full `lake build` unless needed
- CI will perform the complete build after pushing

Full Mathlib builds take 30+ minutes. For draft PRs or conflict resolution, targeted builds are usually sufficient.

## Fetching the Cache

Before building, always fetch the Mathlib oleans cache:

```bash
lake exe cache get
```

Use `lake exe cache get!` (with `!`) to force re-download if the cache appears corrupt.
