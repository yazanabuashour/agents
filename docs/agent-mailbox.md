# Use the portable mailbox helper

Use an existing authenticated official Resend CLI with native Inboxes access.
Check [official releases](https://github.com/resend/resend-cli/releases) and
command help; stable releases and older previews can lack required capabilities.
The helper checks for draft `--idempotency-key` and thread `--folders` support.
Authentication remains with the stock CLI, via its profile or `RESEND_API_KEY`.

Create a private JSON file outside any Git checkout, with mode `0600`:

```json
{
  "inbox_id": "00000000-0000-4000-8000-000000000001",
  "profile": "work-mail",
  "state_dir": "/absolute/private/path/mailbox-state",
  "archive_dir": "/absolute/private/path/mailbox-archives",
  "download_timeout_seconds": "HOST_MEASURED_POSITIVE_SECONDS"
}
```

Replace the synthetic inbox ID and path with your own. Omit `profile` to use the
stock CLI's default profile/environment. Config selects a mailbox; it grants no
send authority. Use one state directory for every consumer of that inbox/profile.
State and read command output contain private mail; keep them outside public logs.
`archive_dir` is required only for `run`. For `run`/`export`, set a positive numeric
`download_timeout_seconds` from a host transfer measurement. It bounds inactivity
on connect, TLS, and download reads, not total transfer duration; retain the
measurement receipt privately. Scheduled hosts also need a measured invocation
deadline to stop stalled CLI/rsync processes. Set optional `archive_copy` to an
existing rsync destination directory ending in `/`, such as
`backup-host:/absolute/private/path/mailbox-archives/`. It requires rsync and
existing SSH access for a remote destination; omit it for local-only operation.

After the [profile installation](install.md), run:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json doctor
agent-mailbox --config /absolute/private/path/mailbox.json poll
agent-mailbox --config /absolute/private/path/mailbox.json pending
agent-mailbox --config /absolute/private/path/mailbox.json ack EMAIL_ID
agent-mailbox --config /absolute/private/path/mailbox.json review DRAFT_ID LOGICAL_KEY
agent-mailbox --config /absolute/private/path/mailbox.json send LOGICAL_KEY --review-sha256 REVIEW_SHA256 --authorized
agent-mailbox --config /absolute/private/path/mailbox.json reconcile LOGICAL_KEY
agent-mailbox --config /absolute/private/path/mailbox.json run
agent-mailbox --config /absolute/private/path/mailbox.json export --output /absolute/private/path/new-archive
agent-mailbox verify /absolute/private/path/new-archive
```

Use `resend inboxes drafts create|get|update` for drafting. Each operation locks
that state without waiting: overlap exits `75` before mailbox work. A completed
discovery pass starts again at the newest threads on the
next poll, covering inbox, archive, spam, sent, and trash. Message IDs deduplicate
across folders, passes, and restarts. Pending items remain until acknowledged;
save processing results before acknowledging them. If a saved cursor becomes
invalid,
run `restart-discovery` to clear only scan progress; pending mail, acknowledgement,
and send history remain. Deleted mail cannot be recovered by restarting.

The saved discovery cursor resumes an interrupted traversal. Messages that move
or arrive ahead of that cursor may be found on the next full pass. Server
retention/deletion and changes during pagination can hide mail; discovery is
neither an atomic snapshot nor an exactly-once consumer scheduler.

A logical send key is bound to one native draft and full reviewed snapshot.
Review rejects reusing a key for another draft or a draft with another key.
If an unsent draft changes, preserve the human changes: run `review` with the same
draft and key again and inspect the fresh snapshot. Do not erase attempted
intents to enable retries. There is no automated reset for ambiguous sends.

Send requires the SHA-256 from the snapshot that this caller inspected, rejects
a review replaced by another caller, and re-fetches the draft and sender before
persisting an attempt and calling
the stock CLI with a deterministic idempotency key. A crash or unexpected result
leaves a durable attempted/uncertain intent. Reconciliation reads native draft
`email_id`/`thread_id`; missing or deleted drafts remain unresolved. Neither a
local lock nor the API's 24-hour key lifetime removes the human UI race between
comparison and send. A recorded sent receipt means queued, not delivered.

See [the mailbox skill](../skills/agent-mailbox/SKILL.md) for authority, recipient,
attachment, and mutation boundaries. The helper is a durability aid, not a
permission system, daemon, routing service, or credential manager.

## Run a read/archive job

`run` checks capabilities, discovers messages, and exports all five native folders,
labels, and native drafts into a new dated directory under `archive_dir`. It does
not send, draft, acknowledge processing, mark read, move, or delete server mail.
Output reports a pending count and archive status rather than bodies. Config,
state, and archive paths are selected explicitly; the helper does not discover
credentials, provision routes, create a daemon, or install scheduled jobs.

If `archive_copy` is set, the same invocation copies each locally verified archive
with rsync's checksum transfer. The destination and authentication belong to the
host. Local archives survive a copy failure. Transfer success is not an independent
remote manifest rehash or a restore proof; verify the retained copy on that host
when establishing the backup workflow. Copying is optional for public consumers.

## Verify exported bytes

`export` creates a new private directory and retains bodies as UTF-8 files, native
and provider JSON metadata, available incoming raw MIME, and downloaded attachment
bytes under ID-based filenames. It paginates threads, messages, drafts, and
attachments. Labels are an unpaginated native endpoint. It traverses all five
folders explicitly, including spam and trash, and deduplicates repeated message
IDs. Attachment names are metadata only; the helper never runs attachments or
uses their names as filesystem paths. Download requests use provider-generated
HTTPS URLs, all validated public address alternatives, system TLS verification,
a configured inactivity timeout, and no redirects
or API credentials. Signed download URLs are removed from archived metadata.

Native inbox IDs are not assumed interchangeable with public Receiving IDs. The
helper tries the corresponding sent/received endpoint for each message and
requires a matching ID and Message-ID before downloading content. Missing access
or incompatible IDs produce a gap. Sent email metadata can include delivery
status and bodies, but the API does not document original sent MIME/full headers;
this is a declared format limit. Incoming MIME and headers are retained when the
Receiving endpoint exposes them. The export is not a full account history, atomic
snapshot, or server restore format.

The manifest hashes actual saved bytes with SHA-256 and records sizes, explicit
gaps, and format limits. `enumeration_complete` covers native traversal;
`content_complete` means all supported content was obtained, subject to the
declared limits. A hash manifest detects changes relative to that manifest; it
is not signed provenance. `verify` checks local file membership, sizes, and hashes
without credentials or network. It reports integrity separately from completeness.

Exit `0` means the operation succeeded. `run`/`export` exit `2` when supported
content or enumeration is incomplete; their JSON describes the retained archive.
Other errors exit `1`, and overlapping state users exit `75`. `verify` exits `0`
when the manifest bytes match, even for an explicitly partial archive. Interrupted
exports retain a manifest marked incomplete; start a fresh export rather than
claiming the partial directory is complete. No export authorizes deletion.
