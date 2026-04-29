# pi Tool Reference

Skills in this repository use pi tool names directly.

| Need | pi tool / command |
|------|-------------------|
| Read file contents | `read` |
| Create or overwrite files | `write` |
| Edit files by exact replacement | `edit` |
| Run shell commands | `bash` |
| Search files | `bash` with `rg`, `grep`, `find`, or `ls` |
| Invoke a skill | `read` the skill's `SKILL.md`, or use `/skill:name` |
| Track work | inline checklist, a TODO file, or a pi extension if installed |
| Dispatch subagents | `subagent` from the `pi-subagents` extension |
| Parallel subagents | `subagent({ tasks: [...] })` |
| Async subagents | `subagent({ agent: "worker", task: "...", async: true })` plus `subagent({ action: "status", id })` |

## Context Files

pi loads `AGENTS.md` context files from:

- `~/.pi/agent/AGENTS.md` (global)
- parent directories walked up from `cwd`
- the current directory

Disable with `--no-context-files` (or `-nc`).

## Skill Locations

pi discovers skills from:

- `~/.pi/agent/skills/` and `~/.agents/skills/` (global)
- `.pi/skills/` and `.agents/skills/` walked up from `cwd` (project)
- package skills and settings-defined paths
- explicit `--skill <path>` CLI flags

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

See the `pi-subagents` skill (`/skill:pi-subagents`) for chains, builtins, and control signals.

## Read-only Research

pi has no built-in plan mode. When a workflow says to plan first, announce that you are researching read-only and stick to `read` + non-mutating `bash` commands until you have a plan to present.

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
