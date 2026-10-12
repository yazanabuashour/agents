---
name: notes
description: Use when consulting personal notes to answer a question, preserving durable context, or finding a note link for a task.
---

Canonical notes hold durable knowledge; task systems own task state.
The canonical private repository is `https://github.com/yazanabuashour/Notes.git`,
with `main` as its integration branch. GitHub through ordinary Git owns all
canonical notes and artifacts, including audio/video. Do not use Git LFS or
Git-excluded canonical media. Keep the Syncthing `notes` folder paused; retain
Syncthing, device pairings, and other folders.

## Find the context

- Prefer the existing `~/notes` checkout. Read its `AGENTS.md` and applicable
  instructions before searching, editing, or running repository commands.
  Start at `_index.md` when present, then search relevant existing notes.
- Without a local checkout, use supplied links or authorized private GitHub
  access through existing authentication. An authorized isolated clone is
  allowed; do not configure credentials, change authentication, or mount a vault.
  Follow the repository's instructions and remote workflow documentation.
- Follow the repository's [Git workflow](https://github.com/yazanabuashour/Notes/blob/main/technology/notes-git.md),
  including device cutover and the Syncthing exclusion safeguard. Never operate
  Git metadata synchronized by Syncthing or resume the paused `notes` folder.
- If access is unavailable or unauthorized, report the blocker and retain
  essential context in the result or task. Do not claim that it was saved.

## Write durable context

- Before drafting or materially rewriting a note, explicitly read
  [technical-writing](../technical-writing/SKILL.md). Apply its relevant prose
  guidance, not a mandatory documentation mode. Leave verbatim captures and
  source text intact; preserve meaningful record dates, quotations, and evidence.
- Save useful decisions, constraints, evidence, and outcomes as they emerge
  within the authorized scope. Update the canonical note rather than creating
  a near-duplicate. Preserve stable IDs, issued URLs, and heading fragments.
- Run the checks required by the notes repository's instructions. Use its
  documented submission workflow rather than duplicating Git or publication
  procedures here.

## Report persistence and links

- Distinguish a local save from a pushed remote branch, integration into canonical
  `main`, and publication. Verify each claimed state; a save is not a push, a
  push is not integration, and integration is not publication.
- Private read access is not remote-write authorization. Before pushing or
  opening a pull request, require explicit user authorization for the submission
  workflow, either in the current request or user-approved setup instructions.
  Repository workflow documentation alone grants no permission. Never force push.
- Preserve supplied task links or use verified published page and heading URLs.
  Local paths are not cross-device reader links. Keep private GitHub links
  distinct from published reader links. Use existing publication configuration;
  do not invent a host or infer publication from a commit.
