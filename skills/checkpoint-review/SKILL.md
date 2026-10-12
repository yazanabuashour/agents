---
name: checkpoint-review
description: REQUIRED when checkpoint review is approved or required.
compatibility: Linux; requires the global checkpoint-review command.
---

# Checkpoint review

Use only when the global policy requires or approves review. The main session
must read this skill for each distinct source checkpoint. The caller owns scope
and the starting baseline. The command reviews supplied inputs; it does not
infer authorization from Git status.

## Prepare the approved delta

Preserve the baseline before editing. If the worktree starts clean, record its
HEAD. If it already contains changes, preserve the relevant before-files or
another recoverable baseline outside the worktree. Do not use an older HEAD as
though those earlier changes belong to the new task. Do not alter the real Git
index, create commits, or discard user changes merely to prepare review.

Prepare a Git-compatible patch containing only the approved before/after
changes, including approved new files. A file allowlist alone cannot separate
successive edits to the same file. Use Git's binary patch support, disable
external diff/text conversion, and inspect the patch before sending it. The
command checks that the patch reverses against the current worktree without
applying it. That check detects stale inputs, not missing task changes or user
authorization. If the baseline cannot be recovered, report the scope blocker
rather than silently reviewing all dirty files.

Write a short context file with:

- The requested outcome and behavior that must remain.
- Non-goals, unrelated changes, and accepted decisions with relevant receipts.
- The baseline and how the patch was prepared.
- Gates passed, failed, or unavailable, plus known limitations.

Keep both inputs outside the worktree. Exclude secrets and unrelated content.
Reviewers may inspect repository context, but unrelated findings remain
follow-up work. Reversing an accepted decision requires new concrete failure
evidence, not a repeated preference.

## Dispatch

Run exactly one command from the main session for each distinct source
checkpoint. Use absolute worktree paths. Review shared source once, then
validate each deployed copy with host-specific gates. Review different source
changes separately. Do not start a duplicate while one is in flight.

```sh
"$HOME/.local/bin/checkpoint-review" \
  --repo /absolute/path/to/worktree \
  --patch /absolute/path/to/approved.patch \
  --context /absolute/path/to/task.md \
  --review correctness EFFORT \
  --review simplification EFFORT
```

Choose effort for scope, novelty, and risk. Add `--review test-reduction EFFORT`
when the delta changes tests. Keep correctness and simplification separate.
Use other focuses only for a concrete risk or requirement; see
`checkpoint-review --help`.

Use background execution only when the host tracks completion and automatically
resumes the agent. In Pi, use `bg_start` with title `Checkpoint review: <repo>`.
Dispatch worktree commands sequentially unless measured provider capacity
supports parallel dispatch. Otherwise run synchronously.

While review runs, freeze that worktree and stop other writers. The command
detects changes; it does not lock the tree. Continue independent work or end
the turn and await the completion message. Do not poll in a loop or use bare
sleeps to wait.

## Settle

Read every file printed as `Review report:`; the completion message can be
truncated. Exit zero means reviewers completed, not that findings are absent
or correct. Keep the report and input/model/timing receipt with dispositions.
Do not treat missing token or cost accounting as zero.

Address findings within scope; unrelated findings do not authorize edits.
For test cuts, identify where meaningful input, timing, origin, side-effect,
and failure distinctions survive. Move useful assertions before deleting cases;
no cut is required. Rerun affected gates. Repeat review only for failed or
interrupted execution, concurrent worktree changes, or an approved scope
expansion. An unavailable gate blocks commit, not an honest handoff.
