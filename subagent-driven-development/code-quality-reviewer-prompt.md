# Code Quality Reviewer Prompt Template

This file's content is the **`task` string** for an `agent: "reviewer"`
dispatch (second review pass — code quality).

```typescript
subagent({
  agent: "reviewer",
  task: `<everything below this line, with placeholders substituted>`,
  context: "fresh"
})
```

**Only dispatch after spec compliance review passes.** If spec compliance is
still red, fix that loop first.

**Purpose:** Verify the implementation is well-built — clean, tested,
maintainable, well-decomposed.

---

You are performing a code quality review on a completed task.

## CRITICAL: Read Only. Do Not Edit.

`pi-subagents` describes you as a "review-and-fix specialist", but in this
workflow you must **only report findings**. The worker will fix anything you
flag, then this review re-runs against the new SHAs.

## Use the Standard Code Review Template

Apply the review structure from
`requesting-code-review/code-reviewer.md` (read it if you have
not already), with the inputs below.

```
WHAT_WAS_IMPLEMENTED: [paste worker's summary here]
PLAN_OR_REQUIREMENTS: Task N from [plan file path]
BASE_SHA:             [sha before task]
HEAD_SHA:             [sha after task]
DESCRIPTION:          [one-sentence task summary]
```

Working directory: [absolute path]
Branch:            [branch name]

You can inspect:
- The diff: `git diff <BASE_SHA>..<HEAD_SHA>`
- The current files at HEAD
- Test output for any commands the plan defined

## Standard Code Quality Concerns

Apply your usual checks:

- Correctness — does the code do what its tests claim?
- Robustness — error handling, null safety, concurrency, abort paths
- Tests — are they meaningful, do they assert behavior rather than mock interactions?
- Naming — clear, accurate, consistent
- Style — follows the project's existing patterns and lint rules
- Security — input validation, secret handling, injection surfaces
- Performance — obvious inefficiencies (not premature optimization)

## Plus, Specific to This Workflow

- Does each new or modified file have **one clear responsibility** with a well-defined interface?
- Are units decomposed so they can be **understood and tested independently**?
- Is the implementation following the **file structure from the plan**?
- Did this change create new files that are already large, or significantly grow existing files? (Don't flag pre-existing file sizes — focus on what this commit contributed.)
- Any **dead code** introduced and forgotten (unused exports, commented blocks, leftover scaffolding)?
- Any **leaks across boundaries** the plan tried to set up (e.g. a tracker module reaching into orchestrator state)?

## Report Format

Use the template's output structure:

```
Strengths:
  - <what's done well>
  - ...

Issues:
  Critical:
    - <issue> (file:line) — <why it matters>
  Important:
    - <issue> (file:line) — <why it matters>
  Minor:
    - <issue> (file:line) — <why it matters>

Assessment: APPROVED | NEEDS CHANGES
```

`APPROVED` means no Critical or Important issues. Minor issues may still be
listed for follow-up but should not block. `NEEDS CHANGES` means the worker
must fix the listed Critical / Important items, after which this review
re-runs against the new SHAs.

Be specific: cite file paths and line numbers for every issue. The worker
will fix exactly what you list.
