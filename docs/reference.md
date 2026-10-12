# Profile and workflow reference

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

Technical writing is hidden from automatic model selection by Pi/Claude frontmatter
and Codex's `agents/openai.yaml`. Invoke it explicitly:

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
| `notes` | Your existing Markdown vault, defaulting to `~/notes` |
| `todoist` | Authenticated stock `td` CLI; consult its command help |
| `agent-browser` | Stock CLI, personal persistent-profile configuration, pinned tabs, and an available credential store |
| `checkpoint-review` | Linux, Bash, Git, native Pi, `rg`, `fd` or `fdfind`, and a configured review model |
| `technical-writing` | No executable workflow dependency |

Configure and authenticate tools outside this repository. The browser skill loads
version-matched guidance through `agent-browser skills get core`; its profile,
credential-store, and session-ownership requirements live in the skill rather than
a copied CLI manual.

## Select a checkpoint-review model

Set `REVIEW_MODEL` to an available Pi `provider/model-id` before running
`checkpoint-review`. The command's fallback calls the external `model-role deep`
helper; that helper and its local configuration are not distributed here. Users
of that fallback must retain their helper and its dependencies, including jq.

Follow [the checkpoint skill](../skills/checkpoint-review/SKILL.md) for approved
delta preparation, dispatch, and result handling. Another agent can invoke the
same command, but its reviewer backend remains Pi. No personal Pi extensions are
required: reviewers run with extensions and skills disabled.
