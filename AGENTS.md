# Agent rules for this repository

This file is the entry point for every coding agent (Claude Code, Codex,
Cursor, Copilot, ...) and every engineer working here. Tools that read
`AGENTS.md` pick it up natively; Claude Code reads it through `CLAUDE.md`.

## Floor

The company-wide rules in
[`shieldedtech/agent-rules`](https://github.com/shieldedtech/agent-rules)
apply here in full. This file may add stricter local rules. It must not weaken
the floor.

## Enforced constraints

`.github/agent-rules.json` declares the constraints a hook checks before
`gh pr create` and `gh issue create` run. Edit that file, not this one, to
change what is enforced. Keep this file for guidance a hook cannot check.

## Local rules

Add repository-specific rules below. Keep each rule to one or two lines and
link to the file that holds the detail.

- Every PR body links its issue with `Closes #N` or `Refs #N`.
- Every issue and PR has an assignee at creation.
