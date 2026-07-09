---
name: self-review
description: "Self-review of code changes. Uses git diff and applies review guidelines with the same rigor as reviewing another developer's code. Should be run proactively before completing any task that involved writing or modifying code."
---

# Self-Review

This skill performs a self-review of code changes made during the current session, applying the code-review-guidelines. It should be run before completing any task that involved writing or modifying code files.

## When to Use

This skill should be used proactively before completing any task where code files were written or modified (via Write, Edit, or MultiEdit tools). It does not need to be run for tasks that only involved non-code files (markdown, config, documentation) or tasks that did not involve any file changes.

## Agent

If available, use the `probertson-review:code-reviewer` custom agent to perform the review.

## Guidelines

See the `code-review-guidelines` skill for the review standards to apply. These include both universal rules and project-type-specific rules (front-end, back-end). The guidelines are the single source of truth for what to look for.

## Workflow

### 1. Gather the Diff

Run `git diff HEAD` to capture all uncommitted changes (staged and unstaged) against the last commit. If this produces no output (changes were already committed during the session), fall back to `git diff main...HEAD` to capture all changes on the current branch since it diverged from main. If both produce no output, there is nothing to review — report that and stop.

The diff is the starting point for the review — it tells you what changed. But code doesn't exist in a vacuum: also consider the code that calls the changed code, the code that the changed code calls, and the code that runs afterward using its output. Findings should correspond to the changes, but may identify issues in related code that are introduced or exposed by those changes.

### 2. Review the Diff

**Mindset**: Review the diff as if you are reviewing another developer's code, not your own. You are looking for problems, not confirming correctness. Assume there are issues to find — approach the code with healthy skepticism.

Apply the code-review-guidelines, using the diff as the anchor for what to review. The guidelines cover universal checks, front-end specific rules, back-end specific rules, and what NOT to flag.

### 3. Read Full Files for Context

Read the full files that were changed to understand the surrounding context — callers, callees, and downstream consumers of the modified code. This is essential for catching issues that the diff alone cannot reveal, such as a change that is correct in isolation but breaks an assumption made by code that uses it.

### 4. Report

Present findings as a prioritized list: must-fix first, then recommended improvements, then optional polish. For each finding, include:
- Specific location (file, line, code snippet from the diff)
- What the issue is
- Why it matters
- What the suggested change would be

If no issues are found, say so clearly. Do not invent issues to justify the review.

Do not make any code changes — the output of the review is a report only.
