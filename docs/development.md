# Develop and validate changes

## Set up and check

Use Linux with Python 3, PyYAML, Bash, Git, ShellCheck, shfmt, and the standard
process tools supplied by procps and util-linux. On Ubuntu:

```sh
sudo apt-get update
sudo apt-get install --yes git python3 python3-yaml shellcheck shfmt procps util-linux
scripts/check
```

The checks validate skill metadata, JSON and YAML files, shell scripts, installer
ownership and backups, checkpoint-review behavior, and Git whitespace. Checkpoint
tests use mock reviewers; they require no Pi account or real provider requests.
CI runs the same command, never installation into a real user profile.

## Validate changed loading behavior

For installation or discovery changes, use a disposable home and configuration
directories. Check the exported baseline and skill files, loading-link targets,
application-owned entries, and manual-only technical-writing invocation. Run
native-agent checks without real accounts or workflow writes; do not infer loading
from an assistant's claim that it read a file.

Keep private prompts, transcripts, credentials, account state, and verification
receipts outside the repository. Record passed, failed, or unavailable gates in
the change description.

## Install checked changes

After checks and any required or approved review, follow the
[installation preview](install.md#inspect-and-install). Compare changed installed
files with source. Start a new session to refresh instructions and discovery;
do not reload existing sessions without approval.
