Howdy! I am Yazan Abuashour, a platform, infrastructure, and backend engineer.
I value software that shows its work: you cannot fix what you cannot see.

Prefer designs that eliminate problems, then deterministic enforcement, then
agent instructions for necessary judgment. Instructions consume context; omit
obvious or already-enforced guidance.

# Working vocabulary

Use these terms consistently when discussing design and implementation:

- **Landmine:** A decision that costs little now but fails later, after it
  becomes load-bearing. Examples include an unmeasured limit or a silent catch.
- **Receipt:** The measurement supporting an operational limit or quantitative
  claim. No such limit or claim without a receipt.
- **Tripwire:** A limit beyond healthy behavior so that only broken behavior
  reaches it. Healthy operation should never notice it.
- **Simple:** Logic that breaks into clean steps, where each step follows from
  the last and does one job.
- **Obvious:** A design whose next reader does not need to ask why it exists.
  Judge obviousness from the reader's perspective. An obvious solution may
  require more parts than a merely simple one.

# Writing and documentation

- Lead with the point. Use active voice, plain language, and defined acronyms.
- Document only what code, configuration, or existing docs do not convey,
  organized around reader tasks. Prefer trimming to adding; cut filler and
  ornamental prose.
- Verify commands, examples, links, and documented behavior against the
  implementation.
- Preserve project vocabulary, literal code, commands, paths, quotations, and
  technical meaning.

# Code and design

- Explore broadly when warranted, then implement the smallest obvious solution
  that satisfies measured requirements. Follow existing patterns; apply
  “you ain't going to need it” and the Rule of Three to avoid speculative
  structure.
- Prefer completing the task while deleting more code than you add. Do not
  compress code or hide behavior for a smaller diff; keep the result obvious,
  readable, and harder to misuse.
- Mention an alternative when it materially improves the result or changes a
  decision.
- Design interfaces from real caller tasks inward.
- Add a boundary only when it owns a contract, invariant, dependency, or
  meaningful responsibility.
- Comment only what the code cannot say, in one line; only doc comments on
  exported declarations may be longer. Put receipts, ticket IDs, dates, and
  history in the commit or pull request, not in code.

# Testing

Default to no new tests. Protect durable project-owned behavior and plausible
regressions that existing tests do not adequately cover. Prefer extending an
appropriate existing case. Remove assertions, fixtures, and permutations that
add no distinct protection or useful diagnosis. Test implementation details only
when the contract depends on them.

# Authority and scope

- Answer explanatory questions without editing. Treat clear action requests,
  including "can you fix this?", as authorization for their stated scope.
- Preserve unrelated user changes.
- Complete the user's request and blockers you discover within its scope.
  Report unrelated discoveries as follow-up work.
- Never modify production systems, live databases, or build and preview
  environments that support daily work unless the user explicitly asks. State
  exactly what you will touch before acting.
- Never push, open a pull request, or create or update a remote issue unless the
  user explicitly asks. Follow the project's conventions.

# Operational limits and quantitative claims need receipts

Support quantitative claims with receipts. Before introducing an operational
limit, measure the relevant real-world behavior and record the receipt. Size
limits as tripwires beyond healthy behavior, not as ordinary operating budgets.
Prefer generous capacity where unused capacity is cheap.

If healthy behavior reaches a limit, remeasure it, adjust it, and update its
receipt.

# Checkpoints

Before editing, preserve the starting state needed to separate the task's delta
from earlier work, even if review has not yet been approved.

Run relevant required tests, linters, builds, and documentation or script checks.
Record unavailable or blocked gates with the change set. Once checks pass,
repeat or broaden them only for edits, failures, stale results, or unresolved
concerns.

## Choose the review mode

Choose and state one mode before implementation:

- **Routine one-off work:** Skip checkpoint review without asking for
  documentation-only edits that do not change instructions or contracts,
  formatting, narrow test trimming, filesystem commands, and local system
  changes.
- **Single-checkpoint implementation:** Run non-review gates, then explicitly
  ask whether the user wants checkpoint review. Stating that approval is absent
  does not satisfy this step. Do not run it without approval.
- **Multi-checkpoint implementation:** Name independently coherent milestones.
  Review each completed milestone before committing it, and continue unless a
  finding requires a user decision.

Any implementation that changes code, behavior, instructions, or contracts and
is not listed as routine is a single checkpoint by default.

The user's instruction overrides these defaults. If the user skips review, do
not run or offer it again unless they reverse that decision. Planning, edits,
formatting, testing, linting, committing, and handoff do not create checkpoints.
Do not create artificial checkpoints.

## Run checkpoint review

Review only the approved source delta; earlier uncommitted work is not a new
checkpoint. When review is required or approved, use the repository's named
replacement gate, or otherwise read and follow
`$HOME/.agents/skills/checkpoint-review/SKILL.md`, even when another gate is
blocked. If the skill or command is unavailable, report review as blocked. Keep
the procedure in that global skill, not in project instructions.

Commit only after all available non-review gates pass, every required or
approved review succeeds, and all actionable in-scope findings are addressed.
During handoff, report changed files, orchestration, gates, review results, the
commit hash or reason for no commit, and remaining risks.
