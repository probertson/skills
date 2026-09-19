---
name: code-review-guidelines
description: "Code review standards — specific issues to flag and guidance on what not to call out when reviewing changes. Use whenever you are reviewing code changes (your own or another developer's)."
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
6. **Test coverage**: Verify that for any new or updated code that involves branching logic, unit tests were added or updated to test the behavior of the code paths
7. **Guidelines for specific project types** Additional rules for specific types of code/repositories. These should be treated as equal to the guidelines here:
   - If the code under review is front-end code (React, CSS), see [front-end-specific.md](./references/front-end-specific.md) for additional guidelines.
   - If the code under review is backend code (NestJS, microservice, REST endpoint), see [back-end-specific.md](./references/back-end-specific.md) for additional guidelines.

### What NOT to Do

- Don't suggest changes outside the scope of the work
- Don't suggest features or new functionality
- Don't recommend dramatic changes to the approach — if the fix is correct but could have been done differently, that ship has sailed. Focus on polishing what exists.
- **Don't flag issues in unchanged lines**: You may read surrounding code for context to understand what the new code does. But only report findings for lines that were _added or modified_ in this branch (lines starting with `+` in the diff). If the same problem exists in nearby unchanged code, you may note it briefly to explain a severity rating (e.g., "the same pattern exists elsewhere in this file but is pre-existing and out of scope"), but do not list it as a standalone finding.
  - Note, however, that if a code change in a modified line of code introduces a potential issue in another part of the code that uses the modified code, that can and should be called out.
