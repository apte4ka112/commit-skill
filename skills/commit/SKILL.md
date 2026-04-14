---
name: commit
description: "Create a commit, push to a feature branch, and open or update a pull request. Use when finishing a task and ready to submit changes."
argument-hint: "[context about changes] [--base <branch>]"
---

# Git Commit & PR Skill

You receive context from the main conversation via `$ARGUMENTS` — a description of what was done. Arguments may also include `--base <branch>` to override the target branch for the pull request.

## Algorithm

### 1. Analyze changes

Run in parallel:
- `git status` — see what's changed
- `git diff` — see content of changes (staged + unstaged)
- `git log --oneline -10` — see commit style in this repo
- `git branch --show-current` — current branch

**IMPORTANT:** Only stage files relevant to the task. Use `git add <file1> <file2> ...` with explicit filenames. NEVER use `git add .` or `git add -A`.

### 2. Determine the base branch

Parse `$ARGUMENTS` for `--base <branch>`. If present, use that value as the base branch.

Otherwise, auto-detect the default branch of `origin`:

```bash
git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@'
```

If auto-detection fails, try these names in order and pick the first one that exists locally or on the remote: `main`, `master`, `develop`, `dev`.

### 3. Determine commit type

Based on the diff and the context from `$ARGUMENTS`, choose a prefix:

| Prefix | When |
|---|---|
| `feat` | New functionality |
| `fix` | Bug fix |
| `refactor` | Refactoring without behavior change |
| `perf` | Performance optimization |
| `style` | Code formatting (whitespace, semicolons) — NOT CSS |
| `build` | Build system changes (webpack, vite, etc.) |
| `ci` | CI/CD configuration (GitHub Actions, Docker) |
| `chore` | Chores not touching src/test (deps, .gitignore) |
| `docs` | Documentation |
| `test` | Test additions or fixes |
| `revert` | Revert a previous commit |

### 4. Branch

Check the current branch:

- **If already on a feature branch** (not the base branch) → use it as-is.
- **If on the base branch** → create a new feature branch:
  - Name format: `{prefix}/{short-kebab-description}` (e.g. `fix/catalog-breadcrumb-error`)
  - Max 54 characters — shorten the description if longer
  - Create and switch: `git checkout -b {branch-name}`

### 5. Commit

Conventional commits format:

```
{prefix}: {short description in English}
```

Examples:
- `fix: resolve memory leak in parser`
- `feat: add quick filters to catalog`
- `refactor: extract auth middleware`

**No `Co-Authored-By`. No mentions of Claude or AI.**

#### Multiple commits

If the changes are logically unrelated, split them into separate commits. For example:
- Refactor + new feature → `refactor: ...` + `feat: ...`
- Bug fix + test → `fix: ...` + `test: ...`

Do not split artificially. One logical block = one commit. If all changes belong to one task, create a single commit.

#### Format

Use HEREDOC:

```bash
git commit -m "$(cat <<'EOF'
{prefix}: {message}
EOF
)"
```

### 6. Push

```bash
git push -u origin {branch-name}
```

### 7. Pull request — create or update

#### Check for an existing PR:

```bash
gh pr list --head {branch-name} --base {base-branch} --json number,title,url --limit 1
```

#### If the PR exists:

Fetch the current description:

```bash
gh pr view {number} --json body --jq '.body'
```

Append new bullets to the Summary section — do not overwrite existing content:

```bash
gh pr edit {number} --title "{new title}" --body "$(cat <<'EOF'
{old description}
{new bullets}
EOF
)"
```

#### If the PR does not exist:

```bash
gh pr create --base {base-branch} --title "{prefix}: {description}" --body "$(cat <<'EOF'
## Summary
- {bullet-point description of changes in English}
EOF
)"
```

After creating or updating the PR, assign yourself:

```bash
gh pr edit {number} --add-assignee "@me"
```

### 8. Report

Show the user:
- Branch name
- Commit (hash + message)
- PR URL
- Status: created or updated
