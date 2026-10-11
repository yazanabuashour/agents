---
name: todoist
description: Use when capturing, finding, ranking, scheduling, updating, or completing personal to-dos, or processing Todoist Inbox.
---

Use the installed `td` CLI. Consult `td --help` and relevant subcommand help
for syntax and output options.

- Todoist owns personal task state; calendar appointments stay separate.
- Ranked work belongs on the Todos board; unresolved captures belong verbatim
  in Inbox. Never invent dates or schedule triggered conditions.
- Keep durable context in canonical notes when available; link relevant notes
  rather than duplicating task status there. Preserve supplied note URLs. If
  notes are unavailable, retain essential context in the task and report any
  unsaved durable context; do not invent links or create a vault.
- Resolve exact IDs before mutations. Verify writes and ordering.
- Reconcile uncertain writes before retrying; recurring completion advances
  an occurrence.
- If the CLI or account is unavailable, report the blocker rather than
  installing software or changing authentication.
