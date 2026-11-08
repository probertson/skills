---
name: code-review-guidelines
description: "Code review standards — specific issues to flag and guidance on what not to call out when reviewing changes. Use whenever you are reviewing changes (your own or another developer's), whatever the file type: markdown, config and agent instructions included."
---

This skill does not define any action to take. It is reference-only content, including:

- a list of specific common issues that we should be flagging in code reviews
- some guidelines of things to avoid doing when conducting a code review

These are a minimum set of rules, and neither of these lists is exhaustive. You should use your knowledge to flag other potential issues with the code that is being reviewed in addition to what is covered in these guidelines.

The project-type-specific rules below assume a TypeScript stack — the front-end examples assume React/CSS and the back-end examples assume NestJS/TypeScript. Adapt them to your own stack (the intent behind each rule generalizes).

## What To Look For

1. **Architectural improvements**: Identify code duplication introduced by the fix. If the same defensive pattern was added in multiple places, suggest extracting a shared utility.
2. **Complex logic that can be simplified**: If the fix introduced convoluted control flow, suggest clearer alternatives.
3. **Comments that shouldn't persist**: Remove "notes to self" comments, ticket references in code (those belong in git history), or explanatory comments that restate what the code does.
4. **Unnecessary type assertions**: Flag any `as any`, `as unknown as X`, or other type casts that could be replaced with proper type narrowing.
5. **Potential regressions or gaps**: Type checking should catch direct callers that break, but look beyond that to indirect consumers (for example, across writes and reads and subsystem boundaries). Does this change existing behavior and/or data shapes? Is it new work that replaces functionality of an existing system (even if the existing system is left in place)? If so, have all the places the previous code touched been updated?
   - **Every other writer and reader.** When the change alters how a field, column, list or set is written or read, search the repository for everything else that writes or reads it, and check that each one still agrees with the new behavior. A rule enforced by one writer and not the others is a gap.
   - **Siblings get the same change.** When the change adds or fixes a rule on one route, branch, arm or caller, find its siblings (the `PATCH` beside the `DELETE`, the write routes beside the read routes, the other arm of a conditional, the other callers of the same function) and check that each gets the same change, or that there is a reason it doesn't. A sibling left without the change is a finding on the change, even though the sibling's own lines are unchanged.
6. **Test coverage**: Verify that for any new or updated code that involves branching logic, unit tests were added or updated to test the behavior of the code paths
7. **Instructions an agent reads are executable**: Any file an agent may load as instructions or context changes what an agent does, the same way code changes what a program does. That includes `CLAUDE.md`, `AGENTS.md`, `SKILL.md`, agent definitions, hook configurations, prompts, and any README or doc a skill tells an agent to read. Review it as code: what will an agent do differently after reading it? Look for commands it will now run, permissions or scope it widens, contradictions with instructions elsewhere, and wording an agent could reasonably take as a directive even though a human would read it as a note. No file is exempt from review because it is "just text"; scale the depth of the review to the change instead.
8. **Guidelines for specific project types** Additional rules for specific types of code/repositories. These should be treated as equal to the guidelines here:
   - If the code under review is front-end code (React, CSS), see [front-end-specific.md](./references/front-end-specific.md) for additional guidelines.
   - If the code under review is backend code (NestJS, microservice, REST endpoint), see [back-end-specific.md](./references/back-end-specific.md) for additional guidelines.

## Reviewing Against the Spec

Code can meet every guideline above and still build the wrong thing. When the review is given a spec (an issue or ticket, its acceptance criteria, a spec or story file, or the task as it was agreed in the session), also check the diff against it:

1. **Missing or partial requirements**: something the spec asked for that the diff doesn't do, or only does for some of the cases the spec names.
2. **Unrequested behavior**: something the diff does that the spec didn't ask for. It may still be fine (a necessary supporting change), but it should be a deliberate choice, not drift.
3. **Requirements implemented wrongly**: the diff appears to address a requirement, but its behavior doesn't match what the spec describes.

Rules for spec findings:

- **Quote the spec line** each finding relies on. A finding you can't tie to a line of the spec is a code-quality finding, not a spec finding.
- **Know which kind of spec you have.** An issue, acceptance criteria, or spec file is a written requirement; hold the code to it. A spec that was assembled from a conversation is looser: frame findings against it as questions ("the session agreed X; the diff does Y — intended?") rather than as definite defects.
- **If no spec was provided, say so** ("No spec provided; spec review skipped"). Never reconstruct requirements from the code itself: that just checks the code against its own reading of itself.

## What NOT to Do

- Don't suggest changes outside the scope of the work
- Don't suggest features or new functionality. (This doesn't stop you from reporting a requirement the spec asked for that is missing: that is a spec finding, not a feature suggestion.)
- Don't recommend dramatic changes to the approach — if the fix is correct but could have been done differently, that ship has sailed. Focus on polishing what exists.
- **Don't flag issues in unchanged lines**: You may read surrounding code for context to understand what the new code does. But only report findings for lines that were _added or modified_ in this branch (lines starting with `+` in the diff). If the same problem exists in nearby unchanged code, you may note it briefly to explain a severity rating (e.g., "the same pattern exists elsewhere in this file but is pre-existing and out of scope"), but do not list it as a standalone finding.
  - Note, however, that if a code change in a modified line of code introduces a potential issue in another part of the code that uses the modified code, that can and should be called out.
