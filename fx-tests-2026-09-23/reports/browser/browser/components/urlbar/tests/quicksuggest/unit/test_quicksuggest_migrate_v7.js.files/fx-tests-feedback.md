## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrate_v7.js --bugs`
- Expected: a "Bugs" block, or an explicit "no sheriff-annotated bug names this test" line.
- Got: exactly the same output as without `--bugs` (29 lines, no bug section at all), so "no bug" is indistinguishable from "the flag was ignored / the live query failed silently".
- Workaround: searched Bugzilla REST by hand.

## Question: "the tests behind this crash signature, per config"

- Command: `fx-tests failures --message "child process hang at shutdown"` returned "No failure matched", while `fx-tests crashes` lists the same string as the top signature (19,349 crashes, 503 tests). The crash signature is only reachable through `crashes`, and `crashes` has no `--tests` to list the tests behind a signature.
- What would have answered it: `fx-tests crashes --signature <s> --tests`, or `failures --message` also matching crash signatures.

## Question: "which workers / which jobs does this failure happen on, against the jobs where it passed"

- Needed the per-worker distribution of a failure to tell a machine-state cause from a load cause. `--task-ids` gives failing tasks only; no way to list the passing runs' tasks, so no denominator. I fetched workerId per failing task from the Taskcluster API by script (103 failures on 22 of the pool's 77 workers), and could not get the passing side.
- What would have answered it: a worker/machine column in `--task-ids`, and a `--passing-task-ids` (or per-worker pass/fail counts per config).
