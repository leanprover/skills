---
name: lean4-bootstrap
description: Handle Lean 4 bootstrapping changes that require update-stage0. Use when making changes to environment extensions, .olean format, interpreter behavior, or any change that affects how stage0 compiles stage1.
---

# Lean 4 Bootstrapping Workflow

**Use this when making changes that require bootstrapping**, such as:
- Changing environment extension types
- Modifying .olean format
- Changing interpreter behavior or `ParserDescr` constructors
- Any change where stage1's stdlib uses a format that stage0 doesn't understand

## The Problem

Lean is bootstrapped: stage0 (pre-built) compiles stage1, which is the "real" compiler. When you change something that affects how code is compiled or serialized, stage0 doesn't know about your changes, so stage1's .olean files may be incompatible.

**Symptoms of bootstrapping issues:**
- Tests crash with segfaults when loading imported modules
- `getSelector` or similar lookups return `none` unexpectedly
- Data in environment extensions appears corrupted
- "Declaration not found" errors for declarations that should exist

## The Solution: Two-PR Workflow

### PR 1: Code Changes + Trigger CI Update

1. **Make your code changes**

2. **Adjust tests to pass during bootstrap transition**
   - Comment out or modify tests that rely on imported data from modules compiled by stage0
   - Add a comment: `-- To be restored after update-stage0`
   - Tests that set their own state in the same file should still work

3. **Trigger CI to perform update-stage0** by adding a comment to `stage0/src/stdlib_flags.h`:
   ```c
   // Trigger update-stage0 for: <brief description of your change>
   ```

4. **Commit and create PR**

5. **CI will automatically:**
   - Build your changes
   - Run `update-stage0`
   - Create a commit "chore: update stage0"

### PR 2: Restore Tests

After the stage0 update is merged:

1. **Restore the commented-out tests**
2. **Remove the trigger comment** from `stage0/src/stdlib_flags.h`
3. **Commit and create PR**

## Local Development Workflow

If you need to test locally before CI:

```bash
# 1. Make your changes and commit
git add -A
git commit -m "feat: <description>"

# 2. Update stage0 locally
make -j -C build/release update-stage0

# 3. Commit the stage0 update
git commit -am "chore: update stage0"

# 4. Rebuild with the new stage0
make -j -C build/release

# 5. Now tests should work
cd tests/lean/run
./test_single.sh your_test.lean
```

## Example: Changing an Environment Extension Type

Suppose you're changing `SimplePersistentEnvExtension Syntax (Option Syntax)` to `SimplePersistentEnvExtension Name (Option Name)`:

### PR 1:
```lean
-- Change the extension type
builtin_initialize myExt : SimplePersistentEnvExtension Name (Option Name) <- ...

-- Adjust test (comment out parts that read imported data)
-- To be restored after update-stage0
-- /-- info: Data: someValue -/
-- #guard_msgs in
-- run_cmd logInfo m!"Data: {myExt.getState ...}"

-- Tests that set their own data still work
set_my_extension someValue  -- This writes to the new extension
#guard_msgs in
run_cmd ...  -- This reads what we just wrote
```

And add to `stage0/src/stdlib_flags.h`:
```c
// Trigger update-stage0 for: change myExt from Syntax to Name
```

### PR 2 (after CI updates stage0):
```lean
-- Restore the test
/-- info: Data: someValue -/
#guard_msgs in
run_cmd logInfo m!"Data: {myExt.getState ...}"
```

## Important Notes

- **Always prefer the two-PR workflow** — it's what CI does anyway
- **The `stdlib_flags.h` comment** is the official way to trigger automatic stage0 updates
- **Modify `stage0/src/stdlib_flags.h`** (not `src/stdlib_flags.h`) — CI overwrites stage0's copy with src's during update
- **Read `doc/dev/bootstrap.md`** for comprehensive bootstrapping documentation
