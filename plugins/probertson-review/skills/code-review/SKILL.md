---
name: code-review
description: "Performs code review of other developers' work."
disable-model-invocation: true
---

# Review a branch / pull request

This skill performs a code review of a developer's work, with the goal of encouraging code quality.

## Agent

Use the `probertson-review:code-reviewer` custom agent

## Workflow

### Operational rules

The intent is for the review to run with the minimum amount of user intervention possible. One common reason for requiring user intervention is when running commands that are outside the current working directory, such as:

- `cd [some other repo directory] && ...`: running a command in another directory, for example to explore related code in another repo
- `git -C [directory] ...`: running a git operation on a different repository than the current working directory
- Other `[command] && [command]` operations

The nature of these commands means they can't be easily pre-allowed in Claude Code settings (without applying blanket "allow" rules that are too permissive). In most cases, the commands in isolation wouldn't require permission. Use the following strategies to isolate commands so they can be properly analyzed:

- use `cd` in isolation to first move into the directory before running another command
- run commands one at a time instead of concatenating them with `&&`

These rules apply to all subagents as well, and you should pass these instructions to them as needed.

### Setup

The review MUST NOT disturb the current working directory, which may have in-progress changes on a different branch. It is CRITICAL to use a git worktree so the review branch can be checked out independently.

1. Change directory (`cd`) to the specified repo.
2. Identify the current repo's root (e.g. via `git rev-parse --show-toplevel`).
3. Derive a worktree path by appending `-review` to the repo root path (e.g. `/path/to/my-repo-review`).
4. Fetch all updates from the remote (i.e. `git fetch origin`). DO NOT _only_ fetch the branch under review. We need the latest `origin/main` also so we can do a proper comparison between branches.
5. Does the worktree path from (3) already exist?
   - If NO, create it: `git worktree add <worktree-path> <branch>` then `cd <worktree-path>`.
   - If YES (left over from a previous review), run `cd <worktree-path>` then run `git checkout <branch>`.

### Code Review

1. Use the agent to perform a code review of the changes in the branch (all commits from where the branch was created from the `main` branch). MANDATORY: use `git diff origin/main...origin/branch-name` (triple-dot) to get only the changes introduced by this branch since it diverged from main, excluding any unrelated changes that have been merged to main since then.
2. The agent has specific instructions of what types of issues to look for
3. Pass the full `git diff` output to the agent. Make clear that the diff is the authoritative record of what changed: **findings must be scoped to lines added or modified in the diff** (lines prefixed with `+`). The agent may read full file content for context, but should not report issues found in unchanged surrounding code.

### Reporting

1. The agent creates a report with feedback (overall, and also specific to particular changes).
2. The agent has specific instructions for what to include in the report
3. Write out the report for the user
4. After presenting the report, inform the user that the review worktree at `<worktree-path>` is still available for follow-up questions, and that they can run `/probertson-review:code-review-cleanup <repo>` when they are done.
