# Read, send, and archive mailbox messages

Use `agent-mailbox` with an authenticated official Resend CLI that supports native
Inboxes. Follow the [mailbox skill](../skills/agent-mailbox/SKILL.md) for send
authority, recipients, attachments, and mutation boundaries. The helper supplies
durability, not permission, a daemon, routing, or credential management.

## Configure a mailbox

Check [official releases](https://github.com/resend/resend-cli/releases) and CLI
help; stable releases and older previews can lack required capabilities. Inboxes
requires private-beta access and full-access credentials. The helper checks draft
`--idempotency-key` and thread `--folders` support and rejects known broken folder
filter previews. Authentication stays with the stock CLI, through its profile or
`RESEND_API_KEY`.

Create a private JSON file outside any Git checkout, with mode `0600`. The helper
validates the object, required fields, and command-specific settings before opening
state or contacting Resend. Invalid settings report a JSON error with exit `1`:

```json
{
  "inbox_id": "00000000-0000-4000-8000-000000000001",
  "profile": "work-mail",
  "state_dir": "/absolute/private/path/mailbox-state",
  "archive_dir": "/absolute/private/path/mailbox-archives",
  "download_timeout_seconds": "HOST_MEASURED_POSITIVE_SECONDS"
}
```

Replace the synthetic inbox ID and paths with your own. Omit `profile` to use the
CLI's default profile/environment. Use one state directory for every consumer of
that inbox/profile. Config selects a mailbox; it grants no send authority. Keep
config, state, archives, and read output out of public logs and Git checkouts.

`archive_dir` is required only for `run`. For `run` or `export`, replace
`download_timeout_seconds` with a positive number from a host transfer measurement.
It bounds inactivity on connect, TLS, and download reads, not total transfer time.
Retain the measurement receipt privately. Scheduled hosts also need a measured
invocation deadline to stop stalled CLI or rsync processes.

After [installing the profile](install.md), check access:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json doctor
```

Use `agent-mailbox --help` and subcommand help for syntax. Every configured
operation locks the state without waiting; overlap exits `75` before mailbox
work. `verify` needs neither config nor state.

## Read and acknowledge messages

```sh
agent-mailbox --config /absolute/private/path/mailbox.json poll
agent-mailbox --config /absolute/private/path/mailbox.json pending
```

`poll` discovers inbox, archive, spam, sent, and trash messages. Message IDs
deduplicate across folders, passes, and restarts. Pending items remain until
acknowledged. Save processing results durably before acknowledging an item:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json ack EMAIL_ID
```

An interrupted traversal resumes its saved cursor. Messages that move or arrive
ahead of that cursor may be found on the next full pass; a completed pass restarts
at the newest threads on the next poll. Server retention/deletion and pagination
changes can hide mail. Discovery is neither an atomic snapshot nor an exactly-once
consumer scheduler.

If a saved cursor becomes invalid, clear only scan progress:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json restart-discovery
```

Pending mail, acknowledgement, and send history remain. Restarting cannot recover
deleted mail.

## Review and send a native draft

Use `resend inboxes drafts create|get|update` to draft. Obtain or identify user
authority for the actual send before proceeding; reading, drafting, and stored
review material do not grant it.

Choose a stable logical send key and inspect the full returned snapshot:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json review DRAFT_ID LOGICAL_KEY
```

The key binds to one draft and full reviewed snapshot. Review rejects reusing a
key for another draft or a draft with another key. If an unsent draft changes,
preserve the human changes: review the same draft and key again and inspect the
fresh snapshot. Use the SHA-256 from the snapshot this caller inspected:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json send LOGICAL_KEY --review-sha256 REVIEW_SHA256 --authorized
```

Send rejects a review replaced by another caller, re-fetches the draft and sender,
and persists an attempt before calling the CLI with a deterministic idempotency
key. A crash or unexpected result leaves an attempted/uncertain intent. Reconcile
that intent rather than retrying, erasing it, cloning the draft, or inventing a key:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json reconcile LOGICAL_KEY
```

An id-only send response is accepted only when the known native draft confirms
that ID as its sent `email_id` and supplies a valid `thread_id`. Failed or
mismatched readback leaves the attempt uncertain and blocks retries.

Reconciliation reads native draft `email_id`/`thread_id`; missing or deleted drafts
remain unresolved. There is no automated reset for ambiguous sends. Neither the
local lock nor the API's 24-hour key lifetime removes the human UI race between
comparison and send. A recorded sent receipt means queued, not delivered.

## Export or run a read/archive job

For a one-time export, choose a new private directory:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json export --output /absolute/private/path/new-archive
```

For a read/archive job, set `archive_dir` in config and run:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json run
```

`run` checks capabilities, discovers messages, and exports all five native folders,
labels, and native drafts into a new dated directory under `archive_dir`. It reports
a pending count and archive status rather than bodies. It does not send, draft,
acknowledge processing, mark read, move, or delete server mail. The helper does not
install scheduled jobs or discover config, credentials, or archive destinations.

For optional copying, set `archive_copy` to an existing rsync destination directory
ending in `/`, such as `backup-host:/absolute/private/path/mailbox-archives/`.
This requires rsync and existing SSH access for a remote destination. `run` copies
the locally verified archive with rsync's checksum transfer. Local archives survive
a copy failure. Transfer success is not an independent remote manifest rehash or
restore proof; verify the retained copy on that host when establishing the backup
workflow. Omit `archive_copy` for local-only operation.

### Export contents and limits

Exports retain bodies as UTF-8 files, native and provider JSON metadata, available
incoming raw MIME, and downloaded attachment bytes under ID-based filenames.
The helper paginates threads, messages, drafts, and attachments; labels use an
unpaginated native endpoint. It traverses all five folders, including spam and
trash, and deduplicates repeated message IDs.

Attachment names are metadata only, never filesystem paths or executable input.
Downloads use provider-generated HTTPS URLs, validated public address alternatives,
system TLS verification, and the configured inactivity timeout. They send no API
credentials and refuse redirects. Signed download URLs are removed from metadata.

Native inbox IDs are not assumed interchangeable with public Receiving IDs. The
helper tries the corresponding sent/received endpoint for each message and
requires a matching ID and Message-ID before downloading content. Missing access
or incompatible IDs produce a gap. Sent metadata can include delivery status and
bodies, but the API does not document original sent MIME/full headers; this is a
declared format limit. Incoming MIME and headers are retained when exposed by the
Receiving endpoint. An export is not a full account history, atomic snapshot, or
server restore format. No export authorizes deletion.

## Verify archived bytes

```sh
agent-mailbox verify /absolute/private/path/new-archive
```

The manifest records SHA-256 hashes of saved bytes, sizes, gaps, and format limits.
`enumeration_complete` covers native traversal; `content_complete` means all
supported content was obtained, subject to the declared limits. A hash manifest
detects changes relative to that manifest; it is not signed provenance.

`verify` checks manifest structure, local file membership, sizes, and hashes
without credentials or network. Archives may contain only regular files and
directories, never symlinks or special files; the manifest must be a regular file.
Verification fails if any directory cannot be enumerated. It reports integrity
separately from completeness. Interrupted exports retain an
incomplete manifest; start a fresh export instead of treating the partial directory
as complete.

## Exit status

| Status | Meaning |
| --- | --- |
| `0` | Operation succeeded; for `verify`, bytes match even if the archive is explicitly partial |
| `1` | Error |
| `2` | Command-line usage error, or `run`/`export` retained an incomplete archive; inspect usage on stderr or archive JSON on stdout |
| `75` | Another invocation owns the state directory |
