---
description: Enhanced git commit workflow with flag support and conventional commits
argument-hint: [--status | --stage <file> | --commit | --push | --undo | --help]
allowed-tools: Bash(git:*), Bash(python:*), Bash(find:*)
---

# Enhanced Git Commit Workflow

Parse the flag from $ARGUMENTS and execute the matching step below.
If $ARGUMENTS is empty, run the full workflow (steps 1–5 in order).

## Flags

| Flag | Action |
|------|--------|
| `--help` | Show usage and exit |
| `--status` | Show `git status` and `git log --oneline -5` only |
| `--stage <file>` | Stage a specific file; stage all if no file given |
| `--commit` | Commit what is staged (prompts for message) |
| `--push` | Push current branch to origin |
| `--undo` | Undo the last commit (keeps changes staged) |

---

## --help

Display the flag table above and example commands, then exit.

---

## --status

```
git status
git log --oneline -5
```

Report what is staged, unstaged, and untracked. Note any files that should be in .gitignore (e.g. `.claude/settings.local.json`, `*.pyc`, `expenses.json`).

---

## --stage [file]

If a file argument is provided after `--stage`, stage only that file:
```
git add <file>
```
Otherwise stage everything:
```
git add .
```
Then show `git status` to confirm what is staged.

---

## --commit

1. Verify there is something staged (`git diff --cached --stat`). If nothing is staged, tell the user and exit.
2. Run the test suite to make sure nothing is broken:
   ```
   cd expense-tracker && python -m unittest test_expense_tracker -v
   ```
3. If tests fail, abort and report which tests failed.
4. If tests pass, suggest a conventional commit message based on the staged diff:
   - `feat:` for new functionality
   - `fix:` for bug fixes
   - `docs:` for documentation changes
   - `refactor:` for code restructuring
   - `test:` for test additions or changes
   - `chore:` for build, config, or tooling changes
5. Ask the user to confirm or provide their own message.
6. Commit:
   ```
   git commit -m "<message>\n\nCo-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>"
   ```

---

## --push

1. Check `git status` — warn if there are uncommitted changes.
2. Check the branch is not `main`/`master` being force-pushed.
3. Push:
   ```
   git push
   ```
4. Report the remote URL and commit SHA that was pushed.

---

## --undo

Undo the last commit but keep all changes staged:
```
git reset --soft HEAD~1
```
Show `git status` after so the user can see what was unstaged.

---

## Full Workflow (no flags)

Run steps 1–5 in order:

1. **Test** — `cd expense-tracker && python -m unittest test_expense_tracker -v`
   - Abort if any test fails.
2. **Status** — `git status` — show what will be committed.
3. **Stage** — `git add .` — stage all changes.
4. **Commit** — suggest a conventional commit message, confirm with user, then commit.
5. **Push** — `git push` — push to origin.

---

## .gitignore Recommendations

If `.claude/settings.local.json`, `expenses.json`, or `__pycache__/` are untracked, suggest adding them to `.gitignore` instead of committing them.
