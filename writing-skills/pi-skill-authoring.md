# pi Skill Authoring Notes

pi implements the Agent Skills standard and loads skills with progressive disclosure: names and descriptions are always in context; full `SKILL.md` content is loaded on demand with `read` or `/skill:name`.

## Required Structure

A skill is usually a directory containing `SKILL.md`:

```text
my-skill/
  SKILL.md
  references/
  scripts/
```

`SKILL.md` starts with YAML frontmatter:

```markdown
---
name: my-skill
description: Use when the agent needs this specific workflow or reference
---

# My Skill
```

## pi Validation Rules

- `name` is required, lowercase, max 64 chars, letters/numbers/hyphens only.
- `name` must match the parent directory.
- `description` is required and max 1024 chars.
- Missing description prevents loading; most other issues warn but still load.
- Unknown frontmatter fields are ignored.

## Discovery Locations

pi discovers skills from:

- `~/.pi/agent/skills/` and `~/.agents/skills/`
- `.pi/skills/` and `.agents/skills/` in the current project or ancestors
- package skill directories
- paths in `settings.json`'s `skills` array
- explicit `--skill <path>` flags

Use `/reload` after editing skills in an auto-discovered location.

## Description Guidance

The description decides whether pi loads the skill. Make it trigger-focused:

```yaml
# Good
description: Use when tests have race conditions, timing dependencies, or pass/fail inconsistently

# Poor
description: Helps with tests
```

Keep operational detail in the body, not the description.

## Relative Files

Reference supporting files by relative path from the skill directory:

```markdown
Read `references/api.md` for the full API surface.
Run `scripts/check.sh` when validating this workflow.
```

When using those files, resolve the path against the directory containing `SKILL.md`.

## Tool Names

Use pi tool names in skill instructions: `read`, `write`, `edit`, `bash`, and extension tools such as `subagent` when installed.

## Packaging

Skills can be shared in pi packages. A package may expose skills via conventional `skills/` directories or a `pi.skills` entry in `package.json`.
