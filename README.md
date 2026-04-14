# commit-skill

A [Claude Code](https://docs.claude.com/claude-code) skill that takes your working changes, creates a conventional commit on a feature branch, pushes to `origin`, and opens (or updates) a pull request against your repository's base branch.

## What it does

When invoked, the skill:

1. Analyzes the working tree (`git status` / `git diff`).
2. Detects the base branch automatically (`git symbolic-ref refs/remotes/origin/HEAD`) — or takes it from `--base <branch>`.
3. Picks a conventional-commit prefix (`feat`, `fix`, `refactor`, …) based on the diff and the provided context.
4. If the current branch is the base branch, creates a new feature branch named `{prefix}/{short-kebab-description}`.
5. Stages only the files relevant to the task (never `git add .`).
6. Commits with a conventional-commit message — no `Co-Authored-By`, no AI mentions.
7. Pushes the branch to `origin`.
8. Opens a new PR against the base branch — or appends new bullets to an existing PR for that branch.

## Requirements

- [Claude Code](https://docs.claude.com/claude-code)
- `git` and [`gh`](https://cli.github.com/) available on your `PATH`
- `gh auth login` completed

## Installation

### Option 1 — Install as a plugin (recommended)

From inside Claude Code:

```
/plugin marketplace add apte4ka112/commit-skill
/plugin install commit-skill
```

Invoke the skill with `/commit-skill:commit`.

### Option 2 — Install as a personal skill (copy-paste)

```bash
mkdir -p ~/.claude/skills/commit
curl -fsSL https://raw.githubusercontent.com/apte4ka112/commit-skill/main/skills/commit/SKILL.md \
  -o ~/.claude/skills/commit/SKILL.md
```

Invoke with `/commit` after restarting Claude Code.

### Option 3 — Project-level install

Copy `skills/commit/SKILL.md` into your project's `.claude/skills/commit/SKILL.md` and commit it. The skill will be available only in that project.

## Usage

After you've made changes in a repo, ask Claude:

> /commit fix for broken breadcrumb on invalid catalog pages

Or override the base branch:

> /commit feature flag rollout --base develop

The skill will:

- pick the right prefix (`fix`, `feat`, …),
- create a feature branch if needed,
- commit only the relevant files,
- push,
- and open (or update) a PR against the detected base branch.

## License

MIT — see [LICENSE](LICENSE).