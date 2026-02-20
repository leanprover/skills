# Lean Skills

Official [Agent Skills](https://agentskills.io) for developing with [Lean 4](https://github.com/leanprover/lean4).

These skills help AI coding agents work effectively with Lean 4 code — writing proofs, setting up development environments, debugging toolchain regressions, and following project conventions.

## Cross-platform support

These skills follow the [Agent Skills](https://agentskills.io) open standard and work with:

- **Claude Code** — install as a plugin (see below)
- **Gemini CLI** — install as an extension (see below)
- **Codex CLI** — install skills to `~/.codex/skills/` (see below)
- **Cursor, VS Code Copilot, Amp**, and other compatible tools

## Skills included

| Skill | Description |
|-------|-------------|
| `lean-proof` | Methodology for writing Lean proofs: one step at a time, error priority, hardest case first |
| `lean4-setup` | Setting up a lean4 development environment with elan toolchains |
| `lean-bisect` | Bisecting Lean toolchain versions to find regressions |
| `lean-mwe` | Creating minimal working examples for bug reports |
| `mathlib-build` | Building Mathlib with appropriate verbosity settings |
| `lean-pr` | PR conventions for lean4 and mathlib4 repositories |
| `mathlib-pr` | PR conventions for Mathlib: labels, merge process, queueboard |
| `mathlib-review` | Review guidelines for Mathlib PRs: tools, attributes, style checks |
| `nightly-testing` | Understanding the Lean/Mathlib nightly testing infrastructure |

## Installation

### Claude Code

```
/plugin install lean
```

Skills are then available as `/lean:proof`, `/lean:setup`, etc.

For local testing:
```bash
git clone https://github.com/leanprover/skills.git
claude --plugin-dir ./skills
```

### Gemini CLI

```bash
gemini extensions install https://github.com/leanprover/skills
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
