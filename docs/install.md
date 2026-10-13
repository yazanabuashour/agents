# Install the profile

Use Linux, Git, and Python 3.9+. Install and authenticate each agent through its own
setup process; this profile does neither. You do not need the personal Pi extension
package to load the instructions and skills.

## Inspect and install

Stop other writers to the target files and directories before previewing or
applying an install. The installer requires a trusted, quiescent home: its static
symlink checks do not protect against concurrent directory replacement.

```sh
git clone https://github.com/yazanabuashour/agents.git
cd agents
scripts/install-local
```

Review the preview, `instructions/AGENTS.md`, and the skills you intend to use.
Adapt personal preferences before installing, including notes checkout access,
Todoist board conventions, and browser-session policy. Note edits include
committing and pushing; keep that workflow in the notes repository. Do not add
credentials, private account data, or machine configuration to this checkout.

Install the reviewed files:

```sh
scripts/install-local --apply
```

The installer backs up changed existing files and installs copies into `~/.agents`
and the helpers in `~/.local/bin`. It refuses symlink targets and does not delete
unrelated skills. Read [workflow dependencies](reference.md) before invoking a skill.

## Connect your agents

Inspect existing instruction files, rules, and skill directories first. Back up
and reconcile any existing content; do not replace it blindly. Run the commands
below only for target paths that are absent. `ln -sT` refuses an existing target
rather than creating a link inside an existing directory.

```sh
mkdir -p "$HOME/.pi/agent" "$HOME/.codex" "$HOME/.claude/rules" "$HOME/.claude/skills"
ln -sT ../../.agents/AGENTS.md "$HOME/.pi/agent/AGENTS.md"
ln -sT ../.agents/AGENTS.md "$HOME/.codex/AGENTS.md"
ln -sT ../../.agents/AGENTS.md "$HOME/.claude/rules/personal.md"

for skill in agent-browser agent-mailbox checkpoint-review notes technical-writing todoist; do
  ln -sT "../../.agents/skills/$skill" "$HOME/.claude/skills/$skill"
done
```

Pi and Codex discover `~/.agents/skills` directly. Claude Code uses the individual
links in `~/.claude/skills`; preserve application-owned entries in every skills
directory. Custom Pi or Codex configuration directories need equivalent links
under their configured user directories.

Claude Code reads repository `AGENTS.md` natively when its
[project-instruction settings](https://code.claude.com/docs/en/memory#agents-md)
allow it. The user rule above supplies the global personal baseline because
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

Reconcile any new or retired loading links separately. The installer does not
reload sessions, rewrite agent settings, or remove files retired from the source.
