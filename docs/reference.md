# Profile and workflow reference

## Ownership

This profile owns instructions, skills, and helpers usable from any harness,
including browser policy and technical writing. Calling Pi does not decide
ownership: checkpoint review belongs here; resources that depend on Pi's runtime
or interfaces belong in the [Pi package](https://github.com/yazanabuashour/pi).
Neither repository requires the other or manages accounts, browser profiles, or
machine settings. Upstream tools own their CLI guides and implementations.

## Load instructions and skills

| Source | Installed path | Purpose |
| --- | --- | --- |
| `instructions/AGENTS.md` | `~/.agents/AGENTS.md` | Always-loaded personal baseline through each agent's loading link |
| `skills/<name>/SKILL.md` and sibling files | `~/.agents/skills/<name>/` | Conditional instructions and supporting files |
| `bin/checkpoint-review` | `~/.local/bin/checkpoint-review` | Explicit source-delta review command |
| `bin/agent-mailbox` | `~/.local/bin/agent-mailbox` | Durable mailbox discovery, reviewed native draft sends, and verified exports |

The repository's root `AGENTS.md` governs work on this checkout; it is not the
exported baseline. The installer owns profile copies and individual Claude skill
compatibility links, not whole skills directories or runtime configuration.
Application-owned skills keep their original installers and version requirements.
See [installation and ownership migration](install.md) for receipt and conflict handling.

Pi, Codex, and OpenCode discover `~/.agents/skills` directly. Claude Code uses
per-skill links in `~/.claude/skills`, reconciled from the installed shared collection
by `scripts/install-local`; update the shared file, not a harness copy.
Keep invocation-scoped skills outside these discovery directories and load them
explicitly for the task that needs them.

You can invoke technical writing directly:

| Agent | Command |
| --- | --- |
| Pi | `/skill:technical-writing` |
| Codex | `$technical-writing` |
| Claude Code | `/technical-writing` |

Skill visibility is not an access boundary. Instructions do not grant permission,
provide missing tools, or enforce model behavior.

## Workflow dependencies

| Skill | Required environment |
| --- | --- |
| `notes` | Local `~/notes` directory |
| `todoist` | Authenticated stock `td` CLI; consult its command help |
| `agent-browser` | Stock CLI, personal persistent-profile configuration, pinned tabs, and an available credential store |
| `checkpoint-review` | Linux, Bash, Git, native Pi, `rg`, `fd` or `fdfind`, and a configured review model |
| `agent-mailbox` | Linux, Python 3.9+, official Resend CLI with native Inboxes capabilities, full-access credentials, and private-beta access; optional archive copying uses rsync and existing SSH access |
| `technical-writing` | No executable workflow dependency |

See the [notes skill](../skills/notes/SKILL.md) for local reading, writing, and
save verification.

Configure and authenticate tools outside this repository. Follow the
[browser skill](../skills/agent-browser/SKILL.md) for profile and session policy;
`agent-browser skills get core` supplies guidance for the installed CLI version.

## Select a checkpoint-review model

Set `REVIEW_MODEL` to an available Pi `provider/model-id` before running
`checkpoint-review`. The command's fallback calls the external `model-role deep`
helper; that helper and its local configuration are not distributed here. Users
of that fallback must retain their helper and its dependencies, including jq.

Follow [the checkpoint skill](../skills/checkpoint-review/SKILL.md) to prepare the
approved patch, run reviewers, and handle findings. Any harness can invoke the
command; reviewers use native Pi with extensions and skills disabled.
Git preparation trusts the checkout's Git configuration: reverse-patch checking
can execute configured clean or process filters. This is not sandboxed by the
reviewers' read-only tool list. Inspect untrusted Git configuration before use.
