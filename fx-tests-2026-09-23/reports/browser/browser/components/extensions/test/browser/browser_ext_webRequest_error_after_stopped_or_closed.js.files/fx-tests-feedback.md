## Question: "are any of these failures on revisions pushed after the fix landed?"

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webRequest_error_after_stopped_or_closed.js --history` and `--task-ids --limit 0`
- Expected: a way to see the push date (or at least the revision) of each failing run, so that retriggers on old revisions are not read as new failures.
- Got: `--history` shows 3 failures on 2026-09-14 and `--task-ids` groups them under 2026-09-14, with nothing saying they ran on autoland revisions pushed on 2026-08-15, three weeks before the fix. It reads like the failure came back after the fix.
- Workaround: `fx-tests task <id>` for each of the 31 tasks to get the revision, then the Treeherder push API (`/api/project/<repo>/push/?revision=<rev>`) for each push timestamp.
- What would have answered it: a revision and push-date column in `--task-ids`, or a `--by-push-date` option for `--history`.

## Question: "in how many of this test's failing jobs did a given earlier test fail too?"

- Command: `fx-tests task <id> --profiles --limit 0`, run in a loop over all 31 failing tasks, then grep for the leaker's message.
- Expected: a way to ask, for one test's failing jobs, what else failed before it in the same job.
- Got: one job at a time. Also, the per-test "Passed when the harness reran it." line is left out when the header already says "All N passed when the harness reran them", so a grep for that per-test line undercounts.
- What would have answered it: `fx-tests test <path> --co-failures` (tests failing in the same jobs, with counts), or keeping the per-test retry line in every job.
