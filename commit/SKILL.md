---
name: commit
description: Use when the user asks to "commit", "create a commit", "git commit", "stage and commit", or invokes /skill:commit. Creates git commits following the gitmoji convention.
---

# Commit Skill

Goal: Analyze staged or unstaged git changes and create a commit following the gitmoji convention.

If the user invoked this skill via `/skill:commit <args>`, pi will have appended their arguments as a `User:` message after this skill's content — read those arguments as additional context or instructions for the commit.

## Commit Message Format

Format: `<gitmoji> <type>: <description>`

### Gitmoji Reference

| Gitmoji | Type     | Use Case                 |
| ------- | -------- | ------------------------ |
| ✨      | feat     | New feature              |
| 🐛      | fix      | Bug fix                  |
| ♻️      | refactor | Code refactoring         |
| ✅      | test     | Adding or updating tests |
| 📝      | docs     | Documentation changes    |
| 🔥      | remove   | Removing code or files   |
| 🔧      | chore    | Configuration/tooling    |

> If the project defines its own git workflow (e.g. `agent_docs/git-workflow.md`, `CONTRIBUTING.md`, or rules inside `AGENTS.md` / `CLAUDE.md`), follow that instead of the table above.

## Execution Steps

### Step 1: Analyze Changes

Run these commands (a single `bash` call with `&&` is fine):

- `git status` — check for staged and unstaged changes
- `git diff --cached` — view staged changes
- `git diff` — view unstaged changes
- `git log --oneline -5` — recent commits for style reference

If no changes are staged AND none are unstaged, stop and tell the user there's nothing to commit.

If nothing is staged but there are unstaged changes, ask the user which files to stage (or whether to stage everything modified).

### Step 2: Determine Commit Type and Draft Message

Based on the diff analysis:

1. Identify the primary change type (feat, fix, refactor, test, docs, remove, chore)
2. Select the matching gitmoji
3. Draft a concise description focusing on "why" rather than "what"
4. Keep the subject line under 72 characters

If changes span multiple logical concerns, suggest splitting into separate commits before proceeding.

### Step 3: Confirm with User

Present the proposed commit message and the list of files to be committed, then ask the user to confirm. pi has no structured `AskUserQuestion` tool — just present the proposal in chat and wait for a reply. A clean format:

```
Proposed commit:

  <gitmoji> <type>: <description>

Files:
  - path/to/file1
  - path/to/file2

Reply: "yes" to commit, "edit" to modify the message, or "cancel" to abort.
```

Do not run `git commit` until the user replies with explicit approval.

### Step 4: Create the Commit

1. Stage files if not already staged. Always use specific filenames (`git add path/to/file`). Never use `git add -A` or `git add .` unless the user explicitly requested it.
2. Create the commit using a HEREDOC for proper formatting:

   ```bash
   git commit -m "$(cat <<'EOF'
   <gitmoji> <type>: <description>
   EOF
   )"
   ```

3. Run `git status` afterwards to verify success.

### Step 5: Handle Hook Failures

If pre-commit hooks fail:

1. Summarize the errors (ESLint, Prettier, tests, etc.)
2. Offer to fix the issues automatically
3. Re-stage the fixed files and retry the commit
4. Never use `--no-verify` unless the user explicitly requests it

## Important Rules

- Do NOT add "Co-Authored-By" or "Generated with" lines unless the project explicitly requests them
- Do NOT push to remote unless explicitly asked
- Do NOT use interactive flags (`-i`)
- Do NOT amend previous commits unless explicitly asked — always create new commits
- Do NOT use `--no-verify` or `--no-gpg-sign` unless explicitly asked
- Write clear descriptions focusing on "why" rather than "what"
- Respect the project's existing commit style (check `git log --oneline -20` if unsure)
