---
name: ship-it
description: "Ship committed changes on a branch to an open pull request: self-review the branch diff → (optionally bump a CHANGELOG) → open the PR (`create-pr`) → STOP. Does NOT merge. Use for 'ship this', 'get my changes up', 'open the PR' once the work is committed and tested on a branch."
---

# ship-it — committed changes → open pull request

Your one-command ship. Takes committed, tested changes on a branch and carries
them through self-review to an **open PR** — then stops. This is `create-pr`
**plus** the self-review-first step you always want, and **minus** any merge. You
merge by hand.

## Precondition

Your changes are **committed on a feature branch** and the work is done (built,
tests green, manually checked where it matters). This skill does not write code,
create the branch, or run your test loop — it ships what's committed.

## Authorization — push + PR, but NOT merge

If your workflow stops at commit by default (no push / PR without an explicit
go), **invoking `ship-it` is that explicit go for this task** — it authorizes the
push and the PR-create. It does **not** authorize the merge: this skill stops at
an open PR and hands back so you merge deliberately. When something is genuinely
ambiguous, still confirm.

## References (follow them — don't restate)

- **`self-review`** (or `cli-self-review`) — the pre-push review of the branch
  diff. This skill runs it itself (step 2).
- **`create-pr`** — pushes the branch and opens the PR. This skill invokes it
  after self-review.
- The repo's own conventions (commit format, branch naming, PR title style).

## Workflow

### 1. Pre-ship CHANGELOG check (optional — off by default)

Skip this step unless the repo clearly maintains a `CHANGELOG.md` / `RELEASES.md`
**and** the change is user-facing **and** you've been asked to keep it current.
When all of that holds, add an entry per that repo's convention. Most repos won't
need this — don't invent a changelog where none exists, and never loop a change
or PR over a release note; it's advisory.

### 2. Self-review the diff (just before push)

Invoke **`self-review`** and resolve its findings (fix, or push back with
reasoning). Review the final state you intend to push — the whole branch diff,
not an earlier snapshot. Only move on once it's clean.

### 3. Open the PR → stop

Invoke **`create-pr`**. It pushes the branch and opens the PR with a proper
multi-line description. The remote steps are pre-authorized for this task (see
authorization above), so it proceeds without re-asking.

**Stop there. Do not merge.**

## Hand-off

Report: the PR link + number, and that it's **open and ready for your review /
manual merge**. If a caller wraps this skill, return that so it can prompt the
follow-up (the manual merge).
