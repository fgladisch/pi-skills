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

> If the project defines its own git workflow (e.g. `agent_docs/git-workflow.md`, `CONTRIBUTING.md`, or rules inside `AGENTS.md`), follow that instead of the table above.

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

Use a single `user_select` call as the confirmation step. Do **not** send a separate plain-text confirmation message before the tool call.

Put the proposed commit message and file list directly in the tool prompt, then ask for the decision. Do not run `git commit` until the tool returns.

Call it like this (set `allowCustom: true` so the user can type free-form edit instructions):

```json
{
  "question": "Proposed commit:\n\n  <gitmoji> <type>: <description>\n\nFiles to be committed:\n  - path/to/file1\n  - path/to/file2\n\nCommit with this message and file list?",
  "options": [
    { "label": "Commit as-is" },
    { "label": "Edit the message" },
    { "label": "Edit the file selection" },
    { "label": "Cancel" }
  ],
  "allowCustom": true
}
```

Handle the result based on the `answer` field returned by the tool:

- **`Commit as-is`** → proceed to Step 4.
- **`Edit the message`** → ask the user for the new message via a follow-up prompt (plain message, or another `user_select` call if you have concrete alternatives), then re-run Step 3 with the updated message.
- **`Edit the file selection`** → ask which files to include or exclude, restage accordingly, regenerate the diff summary, and re-run Step 3.
- **`Cancel`** → abort. Do not stage or commit anything. Confirm to the user that nothing was committed.
- **Custom answer** (`wasCustom: true`) → treat the typed text as edit instructions (e.g. "change feat to fix"), apply them, and re-run Step 3.
- **Cancelled** (`answer: null`, `cancelled: true`, e.g. user pressed Esc) → abort like `Cancel`.

Always re-confirm after any edit — never auto-commit a modified proposal.

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
