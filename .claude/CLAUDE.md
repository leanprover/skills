# Lean Skills Plugin

This is an [Agent Skills](https://agentskills.io) plugin for Lean 4 development.
It is **not** a Lean project — there is no `lakefile.lean` or `lean-toolchain`. There is nothing to build.

## Repository Structure

- `skills/*/SKILL.md` — Individual skill files
- `skills/*/tests/*.yaml` — Test cases for each skill
- `scripts/check-validation` — CI script to verify all skills have current test results
- `README.md` — Public-facing documentation

## SKILL.md Format

Each skill needs YAML frontmatter with `name` and `description`, followed by markdown:

```markdown
---
name: skill-name
description: One-line description used by agent tools.
---

# Skill Title

Content...
```

## Adding or Removing Skills

1. Create or remove `skills/<name>/SKILL.md`
2. Update the skills table in `README.md`
3. Add test cases in `skills/<name>/tests/*.yaml`
4. Run tests and commit results to `leanprover/skills-testing`

## Testing

Test infrastructure and results live in [leanprover/skills-testing](https://github.com/leanprover/skills-testing). Clone it into `./skills-testing/` (gitignored):

```bash
git clone https://github.com/leanprover/skills-testing.git
```

### Test case format

YAML files in `skills/<name>/tests/`:

```yaml
repo: "https://github.com/owner/repo.git"
sha: "abc123"
prompt: |
  The prompt to test.
description: "What this tests"
# Optional: subdirectory, claude_flags, timeout (default 600s)
```

### Running tests

```bash
# Run and judge a skill's tests
skills-testing/scripts/run-skill-tests lean-proof
skills-testing/scripts/judge-all
skills-testing/scripts/summary --latest
```

### Checking validation

```bash
# Verify all skills have current satisfactory results
scripts/check-validation
```

CI runs `check-validation` on every push/PR. It checks that:
- Every skill has at least one test
- Results exist and are judged satisfactory
- Results are newer than the last modification of the skill and test files

### Dependencies

`yq` and `jq` must be installed.
