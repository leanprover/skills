# Lean Skills

Official [Agent Skills](https://agentskills.io) for developing with [Lean 4](https://github.com/leanprover/lean4) and [Mathlib](https://github.com/leanprover-community/mathlib4). Published by the [Lean FRO](https://lean-fro.org/).

These skills help AI coding agents work effectively with Lean 4 code — writing proofs, setting up development environments, debugging toolchain regressions, and following project conventions.

## Cross-platform support

These skills follow the [Agent Skills](https://agentskills.io) open standard and work with:

- **Claude Code** — install as a plugin (see below)
- **Gemini CLI** — discovers skills from `.gemini/skills/`
- **Codex CLI** — reads `AGENTS.md` and supports Agent Skills
- **Cursor, VS Code Copilot, Amp**, and other compatible tools

## Skills included

| Skill | Description |
|-------|-------------|
| `lean-proof` | Methodology for writing Lean proofs: one step at a time, error priority, hardest case first |
| `lean4-setup` | Setting up a lean4 development environment with elan toolchains |
| `lean4-bootstrap` | Handling stage0/stage1 bootstrapping changes that require update-stage0 |
| `lean-bisect` | Bisecting Lean toolchain versions to find regressions |
| `lean-mwe` | Creating minimal working examples for bug reports |
| `mathlib-build` | Building Mathlib with appropriate verbosity settings |
| `lean-pr` | PR conventions for lean4 and mathlib4 repositories |

## Installation

### Claude Code

```
/plugin install lean@claude-plugins-official
```

Skills are then available as `/lean:proof`, `/lean:setup`, etc.

For local testing:
```bash
git clone https://github.com/leanprover/lean-skills.git
claude --plugin-dir ./lean-skills
```

### Gemini CLI

```bash
gemini extensions install https://github.com/leanprover/lean-skills
```

### Other tools

Clone this repository and point your tool at the `skills/` directory, or copy the `AGENTS.md` file into your project.

## License

Apache-2.0
