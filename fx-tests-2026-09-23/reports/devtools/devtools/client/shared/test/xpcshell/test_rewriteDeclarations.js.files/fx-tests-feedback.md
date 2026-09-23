## Are the failures concentrated on some workers?

- Command: `fx-tests test <path> --task-ids --limit 0 --json`
- Expected: the worker (machine name) of each failing task, and a way to get the passing runs' tasks/workers of the same config, so failing workers can be compared with the pool.
- Got: task IDs of failing runs only, no worker. Passing runs are not listed anywhere.
- Workaround: one Taskcluster `task/<id>/status` request per failing task for `workerId` (72 requests); no workaround for the passing side, so "is this worker-specific?" stayed unanswered.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/shared/test/xpcshell/test_rewriteDeclarations.js --bugs`
- Expected: a "Bugs: none annotated" line.
- Got: the same output as without `--bugs`; only `--json` shows `"annotatedBugs": []`, so "no bug" and "flag ignored" look the same.
