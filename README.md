# commit-skill

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-D97757?logo=anthropic&logoColor=white)](https://docs.claude.com/claude-code)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-FE5196?logo=conventionalcommits&logoColor=white)](https://www.conventionalcommits.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/apte4ka112/commit-skill?style=social)](https://github.com/apte4ka112/commit-skill/stargazers)

> You make changes. You type `/commit`. Everything else is automatic.

A [Claude Code](https://docs.claude.com/claude-code) skill that commits your changes, creates a feature branch if needed, and opens a pull request. No commit message, no thinking.

## Features

- **Understands from context.** Reads the diff and picks the right conventional commit prefix. You don't describe anything.
- **Splits unrelated changes.** Refactor + fix + test in one edit? You get separate commits with correct prefixes.
- **Works from any branch.** Editing on `main`? It auto-creates a feature branch, moves your changes onto it, and opens the PR against the right base.

## Usage

After making changes:

> /commit

That's it. Optionally add context or override the base branch:

> /commit fixing the catalog breadcrumb --base develop

## Install

### Plugin (recommended)

```
/plugin marketplace add apte4ka112/commit-skill
/plugin install commit-skill
```

### Copy-paste

```bash
mkdir -p ~/.claude/skills/commit
curl -fsSL https://raw.githubusercontent.com/apte4ka112/commit-skill/main/skills/commit/SKILL.md \
  -o ~/.claude/skills/commit/SKILL.md
```

## Requirements

[Claude Code](https://docs.claude.com/claude-code), `git`, and [`gh`](https://cli.github.com/) authenticated.

## License

MIT. See [LICENSE](LICENSE).
