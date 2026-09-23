# fx-tests feedback (test_protocol_types.js)

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/shared/protocol/tests/xpcshell/test_protocol_types.js --bugs` (also with `--progress`)
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test".
- Got: the normal output with no bugs section and no message, exit 0. I couldn't tell "none found" from "query silently failed".
- Workaround: Bugzilla quicksearch via curl, which found bug 1991833, the bug tracking this failure mode (it doesn't name the test).

## Question: "is this job-wide collapse the same event in other jobs, and did the suspected leaker run in the jobs that did not collapse?"

- Commands: `fx-tests test <neighbour> --task-ids`, then `fx-tests task <id>` for each job, then `fx-tests task <id> --passed --limit 0 | grep early_shutdown`.
- Missing: (1) `fx-tests task` doesn't flag a job where most tests TIMEOUT (1152 of 1552 here) as a job-level collapse. A "N% of tests timed out; onset at t=…" line would have saved several steps. (2) There's no way to list *passing* task IDs of a config/chunk. Passing runs are the comparison needed to tell a leaker from a worker problem ("did test X run without the collapse?").
