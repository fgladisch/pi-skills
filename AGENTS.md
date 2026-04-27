# AGENTS.md

Project context for agents working in this repository (`pi-skills`).

## Golden rule: keep skills and `README.md` in sync

The `## Skills` table in `README.md` is the human-facing index of every skill in this repo. It **must** stay in sync with the actual skill directories. Whenever you make any of the following changes, update `README.md` in the same change:

- **Adding a skill** — create `<skill-name>/SKILL.md` *and* add a row to the `## Skills` table in `README.md`. Insert the row in alphabetical order by skill name.
- **Removing a skill** — delete the directory *and* remove its row from the table.
- **Renaming a skill** — rename the directory *and* update the row (name + link target if any).
- **Changing what a skill is for** — if you materially change the `description` in a skill's frontmatter (the "When to use" summary), update the matching row in the table so they tell the same story.

The "When to use" column in `README.md` should be a short, human-readable rephrasing of the skill's frontmatter `description`. It does not have to be a verbatim copy, but it must not contradict it.

### How to verify sync

Before finishing any change that touches skills:

1. List skill directories: `ls -1 ~/.agents/skills | grep -v '^README\.md$\|^AGENTS\.md$\|^\.'`
2. Compare against the rows in the `## Skills` table in `README.md`.
3. Every directory must have exactly one row, and every row must point to an existing directory.
4. For each row, skim the corresponding `SKILL.md` frontmatter `description` and confirm the "When to use" column still matches.

If any of those checks fail, fix `README.md` (or the skill) before considering the task done.

## Skill file conventions

- Each skill lives in its own directory: `<skill-name>/SKILL.md`.
- `SKILL.md` starts with YAML frontmatter containing `name` and `description`. The `description` is what pi shows in `<available_skills>` and is what tells the agent when to invoke the skill — keep it precise and trigger-oriented.
- Supporting files (references, templates, examples) live alongside `SKILL.md` in the same directory and are referenced by relative path.
- See the `writing-skills` skill for the full authoring guide.

## Pi conventions used in this repo

- pi auto-loads `AGENTS.md` (this file) and `CLAUDE.md` as project context. Prefer `AGENTS.md`.
- Skills are discovered from `~/.agents/skills` and `~/.pi/agent/skills`.
- Tool names in skills follow pi's tool set (`read`, `bash`, `edit`, `write`, `mcp`, `subagent`). When porting skills from Claude Code or Superpowers, translate tool names accordingly — `using-superpowers/references/pi-tools.md` has the mapping.
