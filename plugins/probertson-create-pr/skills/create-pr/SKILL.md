---
name: create-pr
description: "Push the current branch and open a GitHub pull request with a full multi-line Markdown description, using the gh CLI. This is the single step of opening the PR ONLY — it does not self-review the diff first and does not handle review feedback afterward. Use when asked to just push a branch and open/raise a PR. For the full flow (self-review → open PR) from committed changes, use `ship-it` instead."
---

# Create a GitHub pull request

Push the working branch and open a PR from the terminal via `gh`, with a proper
multi-line Markdown description. (`gh pr create` takes a full Markdown body via
`--body` — or `--body-file` for longer descriptions.)

## When to use

- You have a branch ready and have been asked to **just** push it and open a
  pull request — nothing more.
- If the ask is broader ("ship this", "get it reviewed and up"), this is only
  the open-the-PR step — use **`ship-it`**, which self-reviews first, then calls
  this skill to open the PR.

## Prerequisites

`gh` installed and authenticated for the relevant GitHub host. The repo's
`origin` remote points at the project, so `gh` infers it — no `--repo` needed.
Verify quickly: `gh auth status`. If `gh` is missing or not authed, run
`gh auth login` (for GitHub Enterprise, `gh auth login --hostname <host>`).

## Remote-mutating — needs authorization

Pushing a branch and opening a PR mutate the remote. Do them only when the user
has asked you to push / open the PR (this skill's premise) or otherwise
authorized it. Committing locally is fine without it; pushing is not.

## Workflow

### 1. Push the branch

First check whether the branch already has an open PR — if it does, `gh pr create`
will error; a plain push updates the existing PR and you're done:

```bash
gh pr list --head "$(git branch --show-current)"
```

Then push:

```bash
git push -u origin HEAD
```

(If the branch was rebased / history rewritten, use
`git push --force-with-lease origin HEAD`.)

Most repos run lint / type-check / tests via a **pre-commit or pre-push hook**, so
the push itself may trigger them — let it run (it can fail or take a while). If
the repo uses **manual** checks instead of git hooks, run those first. Follow the
repo's own convention.

### 2. Pick a title that matches the repo's convention

Check how recent PRs are titled and mirror that style:

```bash
gh pr list --state all --limit 10
```

Many repos use **Conventional Commits** prefixes (`fix(scope): …`,
`feat(…): …`, `test(…): …`, `refactor(…): …`, `ci(…): …`). Match what you see;
don't invent a different scheme.

### 3. Write the description as multi-line Markdown

Compose a real description — what & why, the changes, a test plan, and any notes
for reviewers (collisions, follow-ups, deliberate omissions). Pass it via a
quoted heredoc so newlines and Markdown survive:

```bash
gh pr create \
  --head "$(git branch --show-current)" \
  --base main \
  --title "fix(scope): concise summary" \
  --assignee @me \
  --body "$(cat <<'EOF'
## What & why
...

## Changes
- ...

## Test plan
- ...

## Notes for reviewers
- ...
EOF
)"
```

- `--base` is usually `main` (the repo default); confirm if unsure.
- `--assignee @me` assigns the PR to whoever runs the command. The PR **author**
  is always the creator regardless; this sets the **assignee**.
- Use `'EOF'` (quoted) so `$(…)` / backticks in the body aren't expanded.

**Draft or ready?** Ask the user which they want before creating — don't assume.
A **ready** PR requests review immediately; a **draft** lets you stage and verify
first. For draft, add `--draft` to the command above, then mark it ready later
when the user is happy:

```bash
gh pr ready <PR_NUMBER>
```

### 4. Verify it rendered

```bash
gh pr view <PR_NUMBER>
```

Confirm the title and that the multi-line body rendered (headings, bullets,
code) — not collapsed to one line. Report the PR number + URL to the user.

## Quick command reference

| Need | Command |
|---|---|
| Check for existing PR | `gh pr list --head "$(git branch --show-current)"` |
| Push branch | `git push -u origin HEAD` (or `--force-with-lease` after a rebase) |
| See title conventions | `gh pr list --state all --limit 10` |
| Create PR (multi-line body) | `gh pr create --head <b> --base main --title "…" --assignee @me --body "$(cat <<'EOF' … EOF)"` |
| Mark draft ready | `gh pr ready <N>` |
| View / verify | `gh pr view <N>` |
