## `--bugs` with no matching bug prints nothing

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_connect_and_move_tabs.js --bugs`
- Expected: a "Bugs: none found" line (or the section header with no rows).
- Got: output identical to the run without `--bugs`, exit 0, nothing on stderr. Indistinguishable from the flag being ignored; I re-ran with stdout/stderr split to be sure.
- Workaround: none needed once I trusted it; one wasted call.

## Question: which test failed first in this job (the leaker), and did every later test fail the same way?

- Command: `fx-tests task <id> --profiles`, then a Python loop over `fx-tests task <id> --json` for the 44 failing jobs.
- The default output lists the failing tests in path order with no start time or execution order, so "the first one to fail in this browser" can only be guessed from alphabetical manifest order. Here every job had 12-51 tests failing on the same end-of-test vsync check; the one with a different first message (`browser_ext_commands_execute_page_action.js`, "No anchor node") was the leaker.
- What could have shown it: execution order (or start time) per failure, and a grouping like "43 tests after X failed with the same message" in `fx-tests task`, or in `fx-tests test <path> --task-ids` a column with the first failing test of each job.

## Question: were all failing runs on revisions before a given fix landed?

- Command: none in fx-tests; I used `hg json-pushes?changeset=<rev>` for each task's revision.
- `fx-tests test --task-ids` groups by the day the job ran, not the push date of the revision. Three failures dated 2026-09-14 were jobs on 2026-08-15 revisions, which made the test look like it failed again after the fix.
- What could have shown it: the revision's push date next to each task id (or a `--by-push-date` history).
