# pi-skills

A personal skills library for [pi](https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent). The skills use pi tool names and pi workflows only.

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
| `simplify` | After making code changes, before committing — reviews the diff for reuse, quality, and efficiency in parallel and fixes findings (uses [`pi-subagents`](https://github.com/nicobailon/pi-subagents)) |
| `subagent-driven-development` | When executing implementation plans with independent tasks in the current session (uses [`pi-subagents`](https://github.com/nicobailon/pi-subagents)) |
| `systematic-debugging` | When encountering any bug, test failure, or unexpected behavior |
| `test-driven-development` | When implementing any feature or bugfix |
| `using-git-worktrees` | When starting feature work that needs isolation from the current workspace |
| `using-skills` | When starting any conversation — establishes how to find and use pi skills |
| `verification-before-completion` | Before claiming work is complete, fixed, or passing |
| `writing-plans` | When you have a spec or requirements for a multi-step task |
| `writing-skills` | When creating or editing skills |

## Pi-specific notes

- Project context lives in `AGENTS.md`, pi's preferred project-instructions file.
- The `subagent` tool comes from the [`pi-subagents`](https://github.com/nicobailon/pi-subagents) extension. Several skills (`subagent-driven-development`, `dispatching-parallel-agents`, `simplify`) require it. Verify your setup with `subagent({ action: "doctor" })`.
- Skills use pi tools directly: `read`, `write`, `edit`, `bash`, and extension tools such as `subagent` when installed.

## License

MIT — see [LICENSE](LICENSE).
