---
name: agent-browser
description: Use for browser automation, screenshots, extraction, web-app testing, Electron interaction, and cloud-browser workflows; also when standard web search or retrieval fails or misses content.
compatibility: Requires stock agent-browser CLI configured with the personal profile and pinned tabs.
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
research through browser search and rendered-page retrieval. Use headed browsing
or an existing Chrome DevTools Protocol (CDP) session when visibility or human
assistance is needed. Open saved screenshots with your image-reading tool.
Use desktop screenshot/input tools only when browser commands cannot perform the
interaction.

## Personal profile and session ownership

This policy expects stock CLI configuration to select
`~/.local/share/agent-browser/profiles/personal` and pin tabs, including public
browsing. If that setup is missing, report the blocker rather than changing
configuration or creating a profile. Keep profiles and credentials local.
Use a unique named session per workflow, not just a worktree-derived name when
agents share a worktree.

Only one browser process may own the profile. Coordinate its launch; if occupied,
attach to its owner or wait, never launch another browser against it. During
concurrent use, reserve the owner session for browser lifetime management.
Browse through distinct attached sessions using its `get cdp-url` result with
`--cdp` on every command, including reads and cleanup. On first attachment, use
`--pin-tab open <url>` to navigate the automatically allocated tab; starting with
`tab new` leaves an extra blank tab.

Pinned tabs prevent accidental tab switching, not access to shared authentication
or other tabs. Preserve pre-existing tabs. Attached workflows close only their
own tabs, then their session; the owner closes last. Track created target IDs and
verify those tabs are closed before disconnecting. Never use `close --all` for
workflow cleanup.

For explicitly clean browsing, use a fresh session and an empty JSON file via
`--config` on every command, with inherited browser overrides cleared. Preserve
automations' existing run-local isolation.

For manual sign-in, close the owner after all attached workflows finish, launch
native Chromium with this profile and `--password-store=gnome-libsecret` without
automation/debugging flags, then close it before automated reuse.
