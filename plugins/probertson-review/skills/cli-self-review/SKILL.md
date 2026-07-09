---
name: cli-self-review
description: "Reviews all code changes on the current branch since it diverged from main. Designed for CLI invocation via npm scripts."
---

# CLI Self-Review

This skill reviews all code changes on the current branch, applying the code-review-guidelines. It is designed to be invoked from the command line (e.g. via `npm run ai-self-review`) rather than from within an interactive Claude Code session.

## Agent

Use the `probertson-review:code-reviewer` custom agent to review the current working branch.

Do not make any code changes — the output of the review is a report only.

## Output

Present the agent's findings in full. Do not summarize or omit details. For each finding, include:
- The file path and line number(s)
- The actual code from the diff
- What the issue is and why it matters
- The suggested change (as concrete code, not vague advice)
