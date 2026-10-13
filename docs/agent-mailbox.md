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
  "state_dir": "/absolute/private/path/mailbox-state"
}
```

Replace the synthetic inbox ID and path with your own. Omit `profile` to use the
stock CLI's default profile/environment. Config selects a mailbox; it grants no
send authority. Use one state directory for every consumer of that inbox/profile.
State and command output contain private mail; keep them outside public logs.

After the [profile installation](install.md), run:

```sh
agent-mailbox --config /absolute/private/path/mailbox.json doctor
agent-mailbox --config /absolute/private/path/mailbox.json poll
agent-mailbox --config /absolute/private/path/mailbox.json pending
agent-mailbox --config /absolute/private/path/mailbox.json ack EMAIL_ID
agent-mailbox --config /absolute/private/path/mailbox.json review DRAFT_ID LOGICAL_KEY
agent-mailbox --config /absolute/private/path/mailbox.json send LOGICAL_KEY --review-sha256 REVIEW_SHA256 --authorized
agent-mailbox --config /absolute/private/path/mailbox.json reconcile LOGICAL_KEY
```

Use `resend inboxes drafts create|get|update` for drafting. The helper writes only
its private local SQLite state until an authorized `send`. Each operation locks
that state. A completed discovery pass starts again at the newest threads on the
next poll, covering inbox, archive, spam, sent, and trash. Message IDs deduplicate
across folders, passes, and restarts. Pending items remain until acknowledged;
save processing results before acknowledging them. If a saved cursor becomes invalid,
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
a review replaced by another caller, and re-fetches the draft and sender before persisting an attempt and calling
the stock CLI with a deterministic idempotency key. A crash or unexpected result
leaves a durable attempted/uncertain intent. Reconciliation reads native draft
`email_id`/`thread_id`; missing or deleted drafts remain unresolved. Neither a
local lock nor the API's 24-hour key lifetime removes the human UI race between
comparison and send. A recorded sent receipt means queued, not delivered.

See [the mailbox skill](../skills/agent-mailbox/SKILL.md) for authority, recipient,
attachment, and mutation boundaries. The helper is a durability aid, not a
permission system, daemon, routing service, or credential manager.
