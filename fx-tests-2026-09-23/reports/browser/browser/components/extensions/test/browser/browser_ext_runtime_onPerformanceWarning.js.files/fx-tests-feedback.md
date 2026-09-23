## `fx-tests test` "Issues (first failure per run)" counts a TEST-KNOWN-FAIL as a run's failure
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_runtime_onPerformanceWarning.js`
- Expected: issue 2 ("handleEvent() was unable to perform a11y checks on hidden node ...", 7x) to be a failure mode of its own.
- Got: the message is an `a11yWarn`, logged as TEST-KNOWN-FAIL (seen in the JtjwTmwtT6Wkk8O2Yud_Zg profile). The real UNEXPECTED-FAIL in all 7 runs is issue 1 (the vsync timeout). `fx-tests task --messages` lists it among the failure messages too.
- Workaround: loaded a profile to read the log level. Cost one profile load.

## Question: "did each failing job run on a revision that contains landing X?"
- Commands: `fx-tests test <path> --task-ids --limit 0`, then for each task a Taskcluster API call (GECKO_HEAD_REV + created), lando hg2git, and `git merge-base --is-ancestor`.
- Why: the history showed 3 failures on 2026-09-14, after a fix had landed on 09-04. They turned out to be retriggers on 2026-08-15 pushes. `--task-ids` shows the task date, not the push revision or push date, so a retrigger on an old push looks like a recurrence.
- What would have answered it: the revision (and push date) next to each task id in `--task-ids`, and/or `--history` bucketing by push date rather than task date.

## Question: "is every failing job the same cascade (same leaker)?"
- Command: a loop over 38 `fx-tests task <id> --messages --full-messages --limit 0` calls, grepping for the leaker's message.
- What would have answered it: in `fx-tests test --task-ids`, the job's first failing test (in execution order) next to each task id. `fx-tests task` lists failures alphabetically, so the first failure in time needs the resource-usage profile.
