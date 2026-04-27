# pi Tool Mapping

Skills sometimes reference tool names from other harnesses (Claude Code's `Read`, `Bash`, `Skill`, `Task`, etc.). When you encounter these in a skill, use the pi equivalent:

| Skill references | pi equivalent |
|-----------------|---------------|
| `Read` (file reading) | `read` |
| `Write` (file creation) | `write` |
| `Edit` (file editing) | `edit` |
| `Bash` (run commands) | `bash` |
| `Grep` / `Glob` (search) | `bash` (use `rg`, `grep`, `find`) |
| `Skill` tool (invoke a skill) | `read` the `SKILL.md` directly, or `/skill:name` |
| `WebFetch` / `WebSearch` | Use built-in MCP tools or extensions if configured |
| `TodoWrite` (task tracking) | No first-class equivalent — keep todos inline in your response |
| `Task` tool (dispatch subagent) | `subagent` (from the `pi-subagents` extension) |
| Multiple `Task` calls (parallel) | `subagent({ tasks: [...] })` (parallel mode) |
| Task returns result | Synchronous return from `subagent({...})`, or `subagent({ action: "status", id })` for async runs |
| `EnterPlanMode` / `ExitPlanMode` | No equivalent — stay in the main session |

## Context Files

pi loads `AGENTS.md` (and falls back to `CLAUDE.md`) at startup from:

- `~/.pi/agent/AGENTS.md` (global)
- Parent directories walked up from `cwd`
- The current directory

Disable with `--no-context-files` (or `-nc`). When a skill talks about the user's project memory file (e.g. "CLAUDE.md says X"), in pi that means whichever of `AGENTS.md` / `CLAUDE.md` is loaded for the current project.

## Skill Locations

pi discovers skills from:

- `~/.pi/agent/skills/` and `~/.agents/skills/` (global)
- `.pi/skills/` and `.agents/skills/` walked up from `cwd` (project)
- Any directory listed in `settings.json`'s `skills` array, or `--skill <path>` on the CLI

When a skill says "browse `~/.claude/skills/`", in pi browse `~/.agents/skills/` (or one of the locations above).

## Subagent Dispatch

The `pi-subagents` extension provides the `subagent` tool. Verify it is healthy with:

```typescript
subagent({ action: "doctor" })
```

Common patterns:

```typescript
// Single subagent
subagent({ agent: "worker", task: "<prompt>", context: "fresh" })

// Parallel fan-out
subagent({
  tasks: [
    { agent: "worker", task: "<prompt A>" },
    { agent: "worker", task: "<prompt B>" }
  ],
  concurrency: 2
})

// Worktree-isolated parallel writes
subagent({ tasks: [...], worktree: true })

// Async run + status polling
subagent({ agent: "worker", task: "<prompt>", async: true })
subagent({ action: "status", id: "<run-id>" })
```

See the `pi-subagents` skill (`/skill:pi-subagents`) for the full tool surface, including chains, builtins, and control signals.

## Plan Mode / Read-only Research

pi has no `EnterPlanMode` / `ExitPlanMode` tool. When a skill says "enter plan mode," just announce that you are researching read-only and stick to `read` + `bash` (with non-mutating commands) until you have a plan to present.

## Environment Detection

Skills that create worktrees or finish branches should detect their environment with read-only git commands before proceeding:

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → already in a linked worktree (skip creation)
- `BRANCH` empty → detached HEAD (cannot branch/push/PR)

See `using-git-worktrees` Step 0 and `finishing-a-development-branch` Step 1 for how each skill uses these signals.
