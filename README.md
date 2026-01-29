# Auto Review Check

Automate the PR review loop workflow - implement code, create PRs, wait for reviews, and iterate on feedback without wasting tokens or context.

## Overview

This toolkit provides scripts to efficiently manage the PR review cycle:

- **Poll for comments** without consuming AI context/tokens
- **Wait for CI checks** to complete
- **Fetch and format comments** for review

## Installation

```bash
git clone https://github.com/harivansh-afk/auto-review-check.git
cd auto-review-check
chmod +x scripts/*.sh
```

## Prerequisites

- [GitHub CLI](https://cli.github.com/) installed and authenticated (`gh auth login`)
- [Graphite CLI](https://graphite.dev/docs/graphite-cli) (optional, for `gt` commands)

## Usage

### Poll for New Comments

Wait for new comments on a PR without consuming resources:

```bash
./scripts/poll_pr_comments.sh <owner/repo> <pr-number> [--timeout <minutes>] [--interval <seconds>]
```

**Example:**
```bash
./scripts/poll_pr_comments.sh myorg/myrepo 123 --timeout 30 --interval 60
```

The script:
- Polls GitHub API at specified intervals (default: 30 seconds)
- Returns immediately when new comments are detected
- Exits after timeout with status code 0

### Wait for CI Checks

Wait for GitHub Actions or other checks to complete:

```bash
./scripts/wait_for_checks.sh <owner/repo> <pr-number> [--timeout <minutes>]
```

**Example:**
```bash
./scripts/wait_for_checks.sh myorg/myrepo 123 --timeout 15
```

Exit codes:
- `0` - All checks passed
- `1` - Checks failed or timeout reached

### Fetch PR Comments

Retrieve and format all comments for review:

```bash
./scripts/fetch_pr_comments.sh <owner/repo> <pr-number> [--unresolved-only]
```

**Example:**
```bash
./scripts/fetch_pr_comments.sh myorg/myrepo 123
./scripts/fetch_pr_comments.sh myorg/myrepo 123 --unresolved-only
```

Output includes:
- Inline review comments with file:line locations
- PR-level comments
- Review decision status

## Workflow Example

Full review loop workflow:

```bash
# 1. Create branch and PR with Graphite
gt create --all --message "feat: add new feature"
gt submit --ai --stack

# 2. Wait for CI checks
./scripts/wait_for_checks.sh myorg/myrepo 123 --timeout 15

# 3. Poll for review comments
./scripts/poll_pr_comments.sh myorg/myrepo 123 --timeout 60 --interval 30

# 4. Fetch and review comments
./scripts/fetch_pr_comments.sh myorg/myrepo 123

# 5. Address feedback, then push updates
gt modify --all
gt submit
```

## Quick Reference

| Task                | Command                                                |
| ------------------- | ------------------------------------------------------ |
| Poll for comments   | `./scripts/poll_pr_comments.sh owner/repo 123`         |
| Wait for checks     | `./scripts/wait_for_checks.sh owner/repo 123`          |
| Fetch comments      | `./scripts/fetch_pr_comments.sh owner/repo 123`        |
| Create branch (gt)  | `gt create -am "feat: description"`                    |
| Submit PR (gt)      | `gt submit --ai --stack`                               |
| Amend changes (gt)  | `gt modify -a`                                         |

## Claude Code Skill

This can also be used as a Claude Code skill. Copy the contents to `.claude/skills/implement-review-loop/` in your project.

## License

MIT
