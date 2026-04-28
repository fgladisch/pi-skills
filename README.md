# pi-skills

A personal skills library for [pi](https://github.com/mariozechner/pi-coding-agent), adapted from the Superpowers skills collection. These skills are also compatible with Claude Code, Codex, Copilot CLI, and Gemini CLI — pi-specific instructions are noted explicitly where they differ.

## Installation

Clone into pi's global skills directory:

```bash
git clone https://github.com/fgladisch/pi-skills.git ~/.agents/skills
```

Or, if you want to keep them in pi's dedicated location:

```bash
git clone https://github.com/fgladisch/pi-skills.git ~/.pi/agent/skills
```

pi auto-discovers skills from both locations on startup. Run pi and the skills will appear in `<available_skills>` in the system prompt.

## Skills

| Skill | When to use |
|-------|-------------|
| `brainstorming` | Before any creative work — explores intent, requirements, and design before implementation |
| `commit` | When the user asks to commit changes — creates git commits following the gitmoji convention |
| `dispatching-parallel-agents` | When facing 2+ independent tasks that can run without shared state |
| `executing-plans` | When you have a written implementation plan to execute in a separate session |
| `finishing-a-development-branch` | When implementation is complete and ready for merge / PR / cleanup |
| `grill-me` | When user wants to stress-test a plan, get grilled on their design, or mentions "grill me" |
| `receiving-code-review` | When receiving code review feedback, before implementing suggestions |
| `requesting-code-review` | When completing tasks or major features, before merging |
| `subagent-driven-development` | When executing implementation plans with independent tasks in the current session (uses [`pi-subagents`](https://github.com/nicobailon/pi-subagents)) |
| `systematic-debugging` | When encountering any bug, test failure, or unexpected behavior |
| `test-driven-development` | When implementing any feature or bugfix |
| `using-git-worktrees` | When starting feature work that needs isolation from the current workspace |
| `using-superpowers` | When starting any conversation — establishes how to find and use skills |
| `verification-before-completion` | Before claiming work is complete, fixed, or passing |
| `writing-plans` | When you have a spec or requirements for a multi-step task |
| `writing-skills` | When creating or editing skills |

## Pi-specific notes

- Project context lives in `AGENTS.md` (pi's preferred convention) or `CLAUDE.md` (also accepted) — both are automatically loaded by pi.
- The `subagent` tool comes from the [`pi-subagents`](https://github.com/nicobailon/pi-subagents) extension. Several skills (`subagent-driven-development`, `dispatching-parallel-agents`) require it. Verify your setup with `subagent({ action: "doctor" })`.

## Origin

Most skills in this repository were adapted from the [Superpowers](https://github.com/obra/superpowers) collection by Jesse Vincent and contributors. Files referencing `superpowers:skill-name` still resolve correctly because pi treats the namespace prefix as part of the cross-reference — you can drop the prefix or leave it, both work.

Other skills (e.g. `commit`) are original to this repository.

## License

MIT — see [LICENSE](LICENSE). Skills adapted from [Superpowers](https://github.com/obra/superpowers) additionally retain the upstream project's license terms.
