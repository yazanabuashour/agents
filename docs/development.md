# Develop and validate changes

## Set up and check

Use Linux with Python 3.9+, PyYAML, Bash, Git, ShellCheck, shfmt, and the standard
process tools supplied by procps and util-linux. On Ubuntu:

```sh
sudo apt-get update
sudo apt-get install --yes git python3 python3-yaml shellcheck shfmt procps util-linux
scripts/check
```

`scripts/check` runs these gates:

- `scripts/check-profile`: skill metadata, repository JSON and YAML, and local
  inline Markdown link targets. Inspect heading fragments and external links
  when changing them; the validator checks only local paths.
- `scripts/test-profile`: titled and angle-delimited local links and source skill
  symlink refusal in a disposable checkout.
- Bash syntax, ShellCheck, and shfmt for the shell entry points.
- `scripts/test-install`: discovery, compatibility links, ownership migration,
  add/rename/retire cleanup, local conflicts, receipt validation, backups,
  idempotence, preview immutability, and symlink refusal in disposable homes.
- `scripts/test-agent-mailbox`: durable discovery, reviewed and uncertain sends,
  export integrity, and download safety with offline fixtures.
- `scripts/test-checkpoint-review`: input validation, dispatch, worktree
  protection, and process cleanup with mock reviewers.
- Git whitespace checks for staged and unstaged changes.

CI runs the same command. Checks need no live accounts, provider requests, or
installation into a real user profile. Mock checkpoint tests are not checkpoint
review; follow the baseline's review policy separately.

## Validate changed loading behavior

For installation or discovery changes, use a disposable home and configuration
and state directories. Keep export and compatibility-link discovery in the
installer, not hand-maintained skill lists or dotfiles. Check the exported baseline
and skill files, loading-link targets, ownership receipt, application-owned entries,
and automatic technical-writing eligibility. Exercise cleanup only through recorded
ownership, including local-change conflicts and untouched unowned links. Verify
that notes and Todoist load the sibling writing skill for drafted or materially
rewritten prose, not reads, state changes, or verbatim captures. Preserve unrelated
manual-only invocation policies. Keep skill descriptions limited to triggers.
Run native-agent checks without real accounts or workflow writes; do not infer
loading from an assistant's claim that it read a file.

Keep private prompts, transcripts, credentials, account state, and verification
receipts outside the repository. Record passed, failed, or unavailable gates in
the change description.

## Install checked changes

After checks and any required or approved review, follow the
[installation preview](install.md#inspect-and-install). Compare changed installed
files with source. Start a new session to refresh instructions and discovery;
do not reload existing sessions without approval.
