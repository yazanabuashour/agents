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
| `skills/<name>/` | `~/.agents/skills/<name>/` | Conditional instructions and supporting files |
| `bin/checkpoint-review` | `~/.local/bin/checkpoint-review` | Explicit source-delta review command |

The repository's root `AGENTS.md` governs work on this checkout; it is not the
exported baseline. The installer copies only its source files and owns neither
whole skills directories nor runtime configuration. Application-owned skills keep
their original installers and version requirements.

Technical writing is eligible for automatic use in Pi, Claude Code, and Codex
when drafting or materially rewriting technical prose, including durable notes
and task descriptions. The notes and Todoist skills explicitly load it for those
writes, not reads, state changes, or verbatim captures. You can also invoke it:

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
| `notes` | Existing `~/notes` checkout preferred; Git or private GitHub access with existing authentication for authorized remote work |
| `todoist` | Authenticated stock `td` CLI; consult its command help |
| `agent-browser` | Stock CLI, personal persistent-profile configuration, pinned tabs, and an available credential store |
| `checkpoint-review` | Linux, Bash, Git, native Pi, `rg`, `fd` or `fdfind`, and a configured review model |
| `technical-writing` | No executable workflow dependency |

See the [notes skill](../skills/notes/SKILL.md) for canonical repository access,
submission authorization, and persistence reporting.

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
