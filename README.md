# Lean Skills

Official [Agent Skills](https://agentskills.io) for developing with [Lean 4](https://github.com/leanprover/lean4).

These skills help AI coding agents work effectively with Lean 4 code — writing proofs, setting up development environments, debugging toolchain regressions, and following project conventions.

## Skills included

| Skill | Description |
|-------|-------------|
| `lean-proof` | Writing Lean proofs: one step at a time, error priority, hardest case first |
| `lean-setup` | Setting up a lean4 development environment with elan toolchains |
| `lean-bisect` | Bisecting Lean toolchain versions to find regressions |
| `lean-mwe` | Creating minimal working examples for bug reports |
| `lean-pr` | PR conventions for the lean4 repository |
| `mathlib-build` | Building Mathlib with appropriate verbosity settings |
| `mathlib-pr` | PR conventions for Mathlib: labels, merge process, queueboard |
| `mathlib-review` | Review guidelines for Mathlib PRs: tools, attributes, style checks |
| `nightly-testing` | Understanding the Lean/Mathlib nightly testing infrastructure |

## Installation

### Claude Code

In Claude Code, type `/plugin`, under "Marketplaces" add "https://github.com/leanprover/skills.git", then under "Plugins" install the `lean` plugin.

Or in a terminal
```
claude plugin marketplace add https://github.com/leanprover/skills.git
claude plugin install lean@leanprover
```

### Gemini CLI

```bash
gemini skills install https://github.com/leanprover/skills --path skills
```

### Codex CLI

Inside a Codex session:
```
$skill-installer install https://github.com/leanprover/skills/tree/main/skills/*
```

Or clone and copy the skills:
```bash
git clone https://github.com/leanprover/skills.git /tmp/lean-skills
cp -r /tmp/lean-skills/skills/* ~/.codex/skills/
```

### Other tools

Clone this repository and point your tool at the `skills/` directory.

## Testing

Every skill should be validated by test cases that demonstrate it actually improves agent performance. As base model capabilities increase over time, skills that no longer provide value can be identified and removed.

Each test case is a YAML file in `skills/<name>/tests/` specifying a git repo, commit SHA, and prompt. Test infrastructure and results live in a separate repository: [leanprover/skills-testing](https://github.com/leanprover/skills-testing).

### Checking validation status

```bash
# Check that all skills have current satisfactory results
scripts/check-validation

# Check a specific skill
scripts/check-validation lean-proof
```

This is also run in CI on every push and pull request.

### Running tests

Clone the testing repo into this directory (it's gitignored):

```bash
git clone https://github.com/leanprover/skills-testing.git
```

Then run tests:

```bash
# Run all tests for a skill
skills-testing/scripts/run-skill-tests lean-proof

# Judge completed runs
skills-testing/scripts/judge-all

# View results
skills-testing/scripts/summary --latest
```

Commit results back to skills-testing so CI passes.

### Test case format

```yaml
repo: "https://github.com/owner/repo.git"
sha: "abc123"
prompt: |
  Your prompt here.
description: "What this tests"
# Optional: subdirectory, claude_flags, timeout (default 600s)
```

### Judge verdicts

- **satisfactory** — the skill meaningfully improved the response
- **not_needed** — the base model performed equally well without the skill
- **needs_improvement** — the skill helped but could be better (suggestions provided)

## License

Apache-2.0
