---
name: code-review-cleanup
description: "Clean up the review worktree for a repo after code review from /code-review."
disable-model-invocation: true
---

1. Identify the repo root: find the directory corresponding to the repository specified in $ARGUMENTS. If none is specified, use the worktree path of the review you just completed.
2. Derive the worktree path by appending `-review` to the repo root path.
3. If the worktree path does not exist, inform the user and stop.
4. Remove the worktree: `git worktree remove <worktree-path> --force`
5. Also remove the local copy of the branch: `git branch -d <branch name>`
6. Confirm to the user that the worktree and branch have been removed.
