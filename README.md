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

Inside a Codex session, install individual skills:
```
$skill-installer install https://github.com/leanprover/skills/tree/main/skills/lean-proof
```

Or clone and copy all skills at once:
```bash
git clone https://github.com/leanprover/skills.git /tmp/lean-skills
cp -r /tmp/lean-skills/skills/* ~/.codex/skills/
```

### Other tools

Clone this repository and point your tool at the `skills/` directory.

## License

Apache-2.0
