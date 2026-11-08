---
name: self-review
description: "Self-review of changes, whatever the file type. Uses git diff and applies review guidelines with the same rigor as reviewing another developer's code. Should be run proactively before completing any task that involved writing or modifying files, markdown and config included."
---

# Self-Review

This skill performs a self-review of the changes made during the current session, whatever the file type, applying the code-review-guidelines. It should be run before completing any task that involved writing or modifying files.

## When to Use

This skill should be used proactively before completing any task where files were written or modified (via Write, Edit, or MultiEdit tools). That includes markdown, config and documentation: any text can end up in an agent's context as instructions, so no file type is exempt. Scale the depth to the change (a typo fix in a README gets a quick look), but never skip it. It does not need to be run for tasks that did not involve any file changes.

## Agent

If available, use the `probertson-review:code-reviewer` custom agent to perform the review.

## Guidelines

See the `code-review-guidelines` skill for the review standards to apply. These include both universal rules and project-type-specific rules (front-end, back-end). The guidelines are the single source of truth for what to look for.

## Workflow

### 1. Gather the Diff

Run `git diff HEAD` to capture all uncommitted changes (staged and unstaged) against the last commit. If this produces no output (changes were already committed during the session), fall back to `git diff main...HEAD` to capture all changes on the current branch since it diverged from main. If both produce no output, there is nothing to review — report that and stop.

The diff is the starting point for the review — it tells you what changed. But code doesn't exist in a vacuum: also consider the code that calls the changed code, the code that the changed code calls, and the code that runs afterward using its output. Findings should correspond to the changes, but may identify issues in related code that are introduced or exposed by those changes.

### 2. Gather the Spec

The review also checks the changes against what they were supposed to do (see "Reviewing Against the Spec" in the `code-review-guidelines` skill). If you were handed a spec when this skill was invoked, use that, with the source and label it came with, and skip the search below. Otherwise, collect whatever defined the work in this session, in this order of preference:

1. An issue or ticket the work was for: its description and acceptance criteria.
2. A spec, plan or story file the work was built from.
3. The task as it was agreed in the conversation. Write it up as a short list of the requirements that were settled, and label it as **conversation-derived** so the review treats it as looser than a written spec.

If nothing in the session says what the change was meant to accomplish (only the edits themselves), there is no spec: say so and skip spec review.

When delegating to the agent, pass the spec (and its source) along with the diff. The agent has no view of this session, so it can't find the spec on its own.

### 3. Review the Diff

**Mindset**: Review the diff as if you are reviewing another developer's code, not your own. You are looking for problems, not confirming correctness. Assume there are issues to find — approach the code with healthy skepticism.

Apply the code-review-guidelines, using the diff as the anchor for what to review. The guidelines cover universal checks, front-end specific rules, back-end specific rules, reviewing against the spec, and what NOT to flag.

### 4. Read Full Files for Context

Read the full files that were changed to understand the surrounding context — callers, callees, and downstream consumers of the modified code. This is essential for catching issues that the diff alone cannot reveal, such as a change that is correct in isolation but breaks an assumption made by code that uses it.

### 5. Report

Present findings as a prioritized list: must-fix first, then recommended improvements, then optional polish. For each finding, include:
- Specific location (file, line, code snippet from the diff)
- What the issue is
- Why it matters
- What the suggested change would be
- Its basis: the guideline it applies, the spec line it quotes, or a note that it is your own engineering judgement

If no issues are found, say so clearly. Do not invent issues to justify the review.

Do not make any code changes — the output of the review is a report only.
