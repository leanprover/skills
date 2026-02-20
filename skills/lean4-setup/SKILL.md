---
name: lean4-setup
description: Set up a lean4 repository clone with proper elan toolchains. Use after cloning the lean4 repository, before building.
---

# Lean 4 Repository Setup

**Use this immediately after cloning the lean4 repository, before building.**

## The Problem

When you clone the lean4 repository, you need to set up elan toolchains before building. Without this setup, tests will use whatever global Lean toolchain is installed via elan by default. This causes confusing test failures because:

1. Tests may run against the wrong Lean version
2. The stdlib being tested may not match the compiler being used
3. Error messages will be inconsistent with what the actual built code should produce

## Required Steps

After cloning a lean4 repository (in a directory like `/path/to/lean4-clone`):

```bash
# 1. Create the build directory structure
cd /path/to/lean4-clone
mkdir -p build/release/stage0 build/release/stage1

# 2. Create elan toolchains linking to where the built stages will be
elan toolchain link lean4-CLONENAME /path/to/lean4-clone/build/release/stage1
elan toolchain link lean4-CLONENAME-stage0 /path/to/lean4-clone/build/release/stage0

# 3. Set elan overrides for the appropriate directories
cd /path/to/lean4-clone/tests
elan override set lean4-CLONENAME

cd /path/to/lean4-clone/src
elan override set lean4-CLONENAME-stage0

# 4. Initialize CMake and build
cd /path/to/lean4-clone
cmake --preset release
make -j -C build/release
```

Replace `CLONENAME` with something descriptive (e.g., the directory name or a task identifier).

## Building and Testing

### Building Lean

```bash
make -j$(nproc) -C build/release
```

### Running a Single Test

```bash
cd tests/lean/run
./test_single.sh example_test.lean
```

### Running the Full Test Suite

```bash
make -j -C build/release test ARGS="-j$(nproc)"
```

### Writing Tests

- All new tests should go in `tests/lean/run/`
- These tests don't have expected output files — they run on a success/failure basis
- Use `#guard_msgs` to check for specific messages

## Verification

After setting up the toolchains, verify it worked:

```bash
cd /path/to/lean4-clone/tests/lean/run
lean --version  # Should show the commit hash from your clone
```

The version should match the git commit of your clone, not a release version.

## Cleanup

When done with the clone, remove the toolchains:

```bash
elan toolchain uninstall lean4-CLONENAME
elan toolchain uninstall lean4-CLONENAME-stage0
```

## Technical Details

- **stage0**: The bootstrap compiler used to build the stdlib and stage1
- **stage1**: The "real" compiler that includes the stdlib and is used for testing
- **elan override**: Sets a directory-specific toolchain that takes precedence over the global default
- The `tests/` directory needs stage1 because tests run against the full Lean system
- The `src/` directory needs stage0 because it's rebuilding the stdlib itself
