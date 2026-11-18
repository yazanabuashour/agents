---
name: council
description: Use when asked to run a council that independently rates work by category and iterates until every member reaches a target score.
compatibility: Requires starting independent agents on the chosen models.
disable-model-invocation: true
---

# Council

Independent members rate the subject in each category. You fix the valid
feedback and repeat with fresh members until every member rates every category
at or above the target.

## Set up

Take these from the request. Ask only for members and for a subject the request
does not name.

- **Members:** each a model and optional settings such as thinking effort.
- **Subject:** an app, CLI, service, API, SDK, or other work.
- **Control:** optional. A comparable product or design rated alongside the
  subject for comparison; its scores never count toward the target.
- **Categories:** use the user's. For a subject with a UI, default to
  simplicity, UI/UX, click efficiency, visual design, and
  [layout stability](#layout-stability). Otherwise propose categories and
  confirm them before the first round.
- **Target:** default 9.5 out of 10 from every member in every category.

If the user adds a category mid-run, add it from the next fresh round. Do not
change a brief that is already running.

## Brief each member

Start each member as a new agent every round, with no inherited conversation or
other reports. Send all members the same brief. Ask each member to:

- Exercise every feature, command, endpoint, page, and workflow of the subject
  and any control, not only inspect them.
- Rate each category from 1 to 10 for each, and list every reason the score is
  not higher, even if the list is long.
- Measure where possible, such as steps from named starting points, latency,
  positions, viewport sizes, and focus.
- Separate what they exercised from what they only inspected or could not reach.
- Leave the subject, its source, and real user data unchanged.

Keep members blind. Never mention:

- The target or that scores decide when to stop.
- Earlier rounds, their scores, or their findings.
- Other members, or that more than one member exists.

Give each member its own disposable writable state, including any backend, so
one member's activity cannot affect another's or real data. Have members mark
paths they cannot exercise without shared or real writes as unexercised.

## Run a round

1. Freeze the subject until every report arrives, so all describe the same
   build.
2. Read each report in full. Stop when every member rates the subject at or
   above the target in every category.
3. Address feedback you judge valid. Feedback several members raise is a
   stronger signal. Verify claims against the code before acting; skip
   suggestions that already exist. If no valid fix remains, report the unmet
   scores and ask the user how to proceed; do not rerate an unchanged build.
4. Do not add safety or hardening complexity where the risk is inherently low.
5. When members disagree about the same path, find the difference in what each
   exercised before choosing a fix.
6. Verify each fix in the built subject, reproducing the reported path.
7. Run the project's gates and checkpoint review, then commit. Invoking this
   skill approves checkpoint review for each round. Push and deploy
   only when the user asked for it; before the first time, state the remote,
   branch, and deploy target, and afterward verify the deployed subject matches
   the build.
8. Start the next round with fresh members on the committed build.

Report each round's scores for every member, subject and any control, in one
table.

## Layout stability

Layout stability asks whether what the reader is reading, focusing, or about to
tap stays where they expect. Check:

- **On load:** text reflow, controls moving under the pointer, late content
  pushing the task down.
- **During interaction:** the triggering control stays anchored when panels,
  validation, filters, or results open or close.
- **Across navigation:** after list, detail, and Back, the reader returns to the
  same item, scroll position, expanded state, and keyboard focus; forward
  navigation settles once.

Measure element positions before and after on desktop and mobile widths.
Cumulative Layout Shift (CLS) is supporting evidence, not the score: it excludes
movement right after input. Intentional movement that preserves the reader's
place is fine.
