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

## Record the baseline

Record where the task starts before editing. A clean worktree's baseline is its
HEAD. Otherwise run
`"$HOME/.local/bin/checkpoint-review" baseline --repo /absolute/path/to/worktree`
and keep the printed tree hash; it captures uncommitted and untracked files
without touching the real index. Do not use an older HEAD as though earlier
changes belong to the new task. If no baseline was recorded and earlier changes
cannot be separated, report the scope blocker rather than reviewing them.

Write a short context file with:

- The requested outcome and behavior that must remain.
- Non-goals, unrelated changes, and accepted decisions with relevant receipts.
- The baseline and any `--path` scope.
- Gates passed, failed, or unavailable, plus known limitations.

Keep the context file outside the worktree. Exclude secrets and unrelated content.
Reviewers may inspect repository context, but unrelated findings remain
follow-up work. Reversing an accepted decision requires new concrete failure
evidence, not a repeated preference.

## Dispatch

The command reviews every change from the baseline to the current worktree,
including files a changed ignore rule exposes, and prints the diffstat it sends.
Before running it, decide which of those changes are approved. Limit the diff to
the task's files with repeated `--path PATHSPEC` when others changed. If approved
and unrelated edits share a file, report the scope blocker instead.

Run exactly one command from the main session for each distinct source
checkpoint. Use absolute worktree paths. Review shared source once, then
validate each deployed copy with host-specific gates. Review different source
changes separately. Do not start a duplicate while one is in flight.

```sh
"$HOME/.local/bin/checkpoint-review" \
  --repo /absolute/path/to/worktree \
  --base BASELINE \
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
