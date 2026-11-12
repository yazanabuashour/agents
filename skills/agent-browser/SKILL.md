---
name: agent-browser
description: Use for browser automation, screenshots, extraction, web-app testing, Electron interaction, and cloud-browser workflows; also when standard web search or retrieval fails or misses content.
compatibility: Requires stock agent-browser CLI configured to attach to the shared browser service.
---

# agent-browser

Discovery guidance adapted from [Vercel's agent-browser](https://github.com/vercel-labs/agent-browser),
Copyright 2025 Vercel Inc., under [Apache-2.0](LICENSE). Modified to trim upstream
instructions and add personal browser ownership and authentication policy;
provenance is recorded in [SOURCE.json](SOURCE.json).

Use the installed stock CLI through your shell tool. If it is unavailable, report
the missing dependency rather than installing software or changing authentication.

## Load only the guidance needed

Before browser interaction, load `agent-browser skills get core` once per
workflow. Use `agent-browser skills get core --full` only for missing reference
details; do not preload it. These guides match the installed CLI version.
The personal session rules below override generic profile, isolation, and
cleanup examples in those guides.

For specialized tasks, use `agent-browser skills list`, then
`agent-browser skills get <name>` for the relevant guide: `electron`, `slack`,
`dogfood` (exploratory testing), `derive-client` (API extraction),
`vercel-sandbox`, `protected-vercel-deployments`, or `agentcore`.

## Research and interaction

Prefer agent-browser for browser interaction, not over working search/fetch tools.
When search/fetch tools fail or return incomplete content, continue
research through browser search and rendered-page retrieval. Open saved
screenshots with your image-reading tool. Use desktop screenshot/input tools only
when browser commands cannot perform the interaction. Complete all challenge stages through normal UI. Report stage counts, retries, outcomes, and human assistance by site after the task. Prioritize unblocking yourself first, and if that doesn't work only then request.

## Shared browser

Stock CLI configuration attaches every command to `agent-browser.service`, a
browser that owns the persistent personal profile on a private display. If the
CLI cannot connect, report the service as down rather than changing
configuration. Public browsing uses this login state too.

Use a unique named session per workflow, not just a worktree-derived name when
agents share a worktree. A session's first command allocates and pins its own
tab; navigate it with `open` rather than `tab new`, which leaves an extra blank
tab. Pinning does not isolate authentication, storage, or other tabs. Close only
tabs your workflow created, then your session; `close` disconnects without
stopping the browser. Never use `close --all`.

For explicitly clean browsing, use a fresh session and an empty JSON file via
`--config` on every command, with inherited browser overrides cleared. This
launches a separate temporary browser that protected sites may flag. Preserve
automations' existing run-local isolation.

To sign in, use `agent-browser auth login <name> --credential-provider vault
--item <agent vault item>`; the password goes from the vault to the page, not
through you. For manual sign-in, wait until all browser workflows finish, run
`systemctl --user stop agent-browser.service`, sign in with
`chromium --user-data-dir="$HOME/.local/share/agent-browser/profiles/personal"`
on the desktop, close it, then start the service again.
