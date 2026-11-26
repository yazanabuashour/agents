# agents

Personal instructions, skills, and workflow helpers for Pi, Codex, Claude Code,
OpenCode, and Antigravity. [Pi extensions](https://github.com/yazanabuashour/pi)
live in a separate repository.

## Install and update

Requires Linux, Git, and Python 3.9+. Preview the install plan, check for drift,
or apply it:

```sh
scripts/install-local          # preview
scripts/install-local --check  # exit nonzero if anything is pending
scripts/install-local --apply  # install and reconcile owned paths
```

`scripts/install-local` copies exported files and links each harness to
`~/.agents/AGENTS.md`:

| Source | Installed path |
| --- | --- |
| `instructions/AGENTS.md` | `~/.agents/AGENTS.md` |
| `instructions/codex.md` | `~/.codex/minimal-codex.md` |
| `skills/<name>/**` (with `SKILL.md`) | `~/.agents/skills/<name>/**` |
| `bin/*` (regular files) | `~/.local/bin/*` |

| Harness | Instruction link | Skill discovery |
| --- | --- | --- |
| Pi | `~/.pi/agent/AGENTS.md` | `~/.agents/skills` |
| Codex | `~/.codex/AGENTS.md` | `~/.agents/skills` |
| Claude Code | `~/.claude/rules/personal.md` | Per-skill links in `~/.claude/skills` |
| OpenCode | `~/.config/opencode/AGENTS.md` | `~/.agents/skills` |
| Antigravity | `~/.gemini/AGENTS.md` | Per-skill links in `~/.gemini/config/skills` |

Ownership state lives in `$XDG_STATE_HOME/agents/installed.json` (default
`~/.local/state/agents/installed.json`). Re-running `--apply` reconciles added,
renamed, and retired exports while refusing to overwrite locally modified files.
Existing sessions are not reloaded; start a new session to pick up changes.

## Check and develop

Requires Linux, Bash, Git, Python 3.11+, PyYAML, ShellCheck, and `shfmt`.

```sh
scripts/check
```

### Helpers

- `bin/checkpoint-review`: runs parallel read-only Pi reviewers against a Git
  delta. Override the model with `REVIEW_MODEL=provider/model-id`; see
  `bin/checkpoint-review --help` and [skills/checkpoint-review/SKILL.md](skills/checkpoint-review/SKILL.md).
- `bin/rust-baseline`: read-only check of Cargo projects against shared lint,
  edition, and toolchain settings (`bin/rust-baseline --root "$HOME/repo"`).

## License

[MIT](LICENSE). Upstream skill licenses remain in their respective directories.
