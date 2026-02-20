# Lean Skills Plugin

This is an [Agent Skills](https://agentskills.io) plugin for Lean 4 development.
It is **not** a Lean project — there is no `lakefile.lean` or `lean-toolchain`. There is nothing to build.

## Repository Structure

- `skills/*/SKILL.md` — Individual skill files
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
