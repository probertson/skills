# probertson-skills

A small marketplace of [Claude Code](https://docs.claude.com/en/docs/claude-code) plugins by Paul Robertson, focused on code review and shipping work to GitHub.

Each plugin is independently installable — install just the piece you want.

## Plugins

| Plugin | What it gives you | Depends on |
|---|---|---|
| **probertson-review** | `self-review` (review your own diff), `cli-self-review` (same, for `npm run`-style CLI use), `code-review` + `code-review-cleanup` (review someone else's branch in a throwaway git worktree), `code-review-guidelines` (a generic set of review standards), and the `code-reviewer` agent that applies them | — |
| **probertson-create-pr** | `create-pr` — push the current branch and open a GitHub PR with a real multi-line description, via the `gh` CLI | — |
| **probertson-ship-it** | `ship-it` — one command: self-review the branch diff → (optional CHANGELOG bump) → open the PR. Stops at an open PR; does not merge | probertson-review, probertson-create-pr |
| **probertson-review-hook** | An **opt-in** `PreToolUse` hook that blocks `git commit` until `self-review` has run in the session | probertson-review |

## Install

Add the marketplace, then install the plugins you want:

```
/plugin marketplace add probertson/skills
/plugin install probertson-review@probertson-skills
/plugin install probertson-create-pr@probertson-skills
/plugin install probertson-ship-it@probertson-skills
```

Plugins with dependencies pull them in automatically — installing `probertson-ship-it` also brings `probertson-review` and `probertson-create-pr`.

The commit-blocking hook is intentionally separate so it isn't forced on you:

```
/plugin install probertson-review-hook@probertson-skills
```

## Notes

- **GitHub-first.** The PR/ship skills use the `gh` CLI and GitHub PR conventions. You'll need `gh` installed and authenticated (`gh auth status` / `gh auth login`).
- **Review guidelines are generic.** The front-end examples assume React/TypeScript and the back-end examples assume NestJS/TypeScript, but the intent behind each rule generalizes — adapt them to your stack.
- These skills started life as personal workflow automation and have been genericized for public use. Suggestions and issues welcome.

## License

[MIT](./LICENSE) © H. Paul Robertson
