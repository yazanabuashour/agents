# Install the profile

Use Linux, Git, and Python 3.9+. Install and authenticate each agent through its own
setup process; this profile does neither. You do not need the personal Pi extension
package to load the instructions and skills.

## Inspect and install

Stop other writers to the target files and directories before previewing or
applying an install. The installer requires a trusted, quiescent home: its static
symlink checks do not protect against concurrent directory replacement. Keep its
ownership receipt trusted too; it records ownership, not authenticated provenance.

```sh
git clone https://github.com/yazanabuashour/agents.git
cd agents
scripts/install-local
```

Review the preview, `instructions/AGENTS.md`, and the skills you intend to use.
Adapt personal preferences before installing, including local `~/notes`
organization, Todoist board conventions, and browser-session policy. Do not add
credentials, private account data, or machine configuration to this checkout.

Install the reviewed plan:

```sh
scripts/install-local --apply
```

The installer discovers exports through `skills/*/SKILL.md`, copies the baseline
and exported skill files into `~/.agents`, installs the Codex base prompt into
`~/.codex/minimal-codex.md`, and installs helpers into `~/.local/bin`.
It connects supported harnesses to the baseline through instruction links and
creates individual Claude compatibility links for skills in the resulting
`~/.agents/skills` collection, including application-owned shared skills. It does
not replace `~/.claude/skills` or change application-owned skill content.
Read [workflow dependencies](reference.md) before invoking a skill.

Ownership lives in `$XDG_STATE_HOME/agents/installed.json`, defaulting to
`~/.local/state/agents/installed.json` when the variable is empty or relative.
Keep state outside the checkout. The receipt records installed hashes, modes,
link targets, and installer-created skill directories; it is not a second export
list. Preview validates the entire plan without writing, including state or backups.
Apply backs up changed or removed owned paths and the previous receipt under the
same state directory's `backups/install-*` directory. Writes are atomic per file,
not across the whole plan; use the backup to reconcile an interrupted install.

Subsequent installs update or retire only unchanged owned paths. A local content,
mode, or link change blocks the whole plan and leaves the receipt intact. Save and
reconcile that change before retrying. Missing owned paths can be recreated.
Cleanup removes only empty installer-created skill directories, never unrelated
files or preexisting directories. The installer refuses symlinks at copy targets
and mutable ancestors; shared application skill links are discovery inputs, not
copy destinations.

## Migrate an existing installation

Matching regular profile files can seed ownership automatically. If an unowned
file differs, save it and reconcile it against the source, then move it aside or
make it match before retrying. The installer does not overwrite it or treat a
backup as permission to discard edits.

Matching preexisting Claude skill links remain untouched and **unowned**, even
when they point at a profile export. To transfer links from another installer:

1. Retire that installer's skill-link declarations without removing instruction
   loading links or application-owned entries.
2. Verify which existing links it owned. Remove only those confirmed profile links
   after checking their targets; do not remove the skills directory.
3. Preview and apply here. The installer recreates the missing compatibility links
   and records ownership for future retirement.

Do not remove matching application-owned links merely to adopt them, or delete the
receipt to bypass conflicts. Unowned stale links require their owner's cleanup.

### Transfer instruction-link ownership

Retire the previous installer's declarations first, without deleting live links.
Do not use Chezmoi `destroy` or `purge` for this handoff. Inspect the links against
[the supported targets](#connect-your-agents), then preview and adopt them:

```sh
scripts/install-local --adopt-instruction-links
scripts/install-local --adopt-instruction-links --apply
```

Adoption records matching links in the ownership receipt without replacing them.
It requires the exact relative targets listed below. Without the flag, matching
preexisting instruction links remain untouched and unowned. A regular file,
directory, or different link target blocks the plan; save and reconcile it before
retrying. The flag never adopts Claude skill links or grants
permission to overwrite instructions. Matching regular profile files, including
the Codex base prompt, follow the file migration rules above.

## Connect your agents

The installer creates these links at each harness's default user location:

| Harness | Instruction link | Relative target |
| --- | --- | --- |
| Pi | `~/.pi/agent/AGENTS.md` | `../../.agents/AGENTS.md` |
| Codex | `~/.codex/AGENTS.md` | `../.agents/AGENTS.md` |
| Claude Code | `~/.claude/rules/personal.md` | `../../.agents/AGENTS.md` |
| [OpenCode v2](https://opencode.ai/v2/docs/instructions/) | `~/.config/opencode/AGENTS.md` | `../../.agents/AGENTS.md` |

Custom harness homes are not configured by this installer. Machine preferences,
models, permissions, extensions, credentials, and sessions remain outside this
profile. The optional Codex `model_instructions_file` setting can select the
installed `~/.codex/minimal-codex.md`; the installer does not change that setting.

Pi, Codex, and OpenCode discover `~/.agents/skills` directly. Claude Code uses the
installer's individual links in `~/.claude/skills`; preserve its own entries and
synced directories. Keep application-owned shared skills linked to their owners,
Codex builtins in their native location, and invocation-scoped skills such as
SiftWire outside global discovery. Do not copy skill content into harness-specific
directories.

Claude Code reads repository `AGENTS.md` natively when its
[project-instruction settings](https://code.claude.com/docs/en/memory#agents-md)
allow it. The user rule supplies the global personal baseline because
Claude does not discover instructions under `~/.agents`. No `CLAUDE.md` alias is
needed. Use a Claude Code version with native `AGENTS.md` support.

Start new sessions and confirm that the personal baseline, repository instructions,
and intended skills load. Technical writing is eligible for automatic use; see
[loading behavior](reference.md#load-instructions-and-skills).

## Update

After reviewing incoming changes and running the [development checks](development.md),
preview and reinstall:

```sh
scripts/install-local
scripts/install-local --apply
```

The plan reconciles added, renamed, and retired exports and owned harness
links. It does not reload sessions or rewrite agent settings.
