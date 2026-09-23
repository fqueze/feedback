# fx-tests feedback (test_nesting-03.js)

## Question: "is this failure concentrated on specific CI workers?"
- Command: `fx-tests test devtools/server/tests/xpcshell/test_nesting-03.js --task-ids --limit 0`
- Expected: some way to see the worker (machine) behind each failing run, and the runs per worker.
- Got: task IDs only. Had to query the Taskcluster task status API for each of the 110 failing tasks, then the Treeherder jobs API for every mac debug xpcshell job to get runs per worker.
- Answer that came out of it: all 110 failures were on 22 of 79 macmini-r8 workers; 57 workers ran 1,096 jobs with 0 failures. That is the main finding of the report, and nothing in fx-tests could show it. A `--workers` breakdown (failures and runs per machine_name) would have answered it directly.

## `fx-tests failures --message "child process hang at shutdown"` finds nothing
- Command: `fx-tests failures --message "child process hang at shutdown"` (and `--message "hang at shutdown"`)
- Expected: the tests behind that message, since `fx-tests test` lists it as the Issues message and `fx-tests crashes` has it as the top signature (19,591 crashes, 507 tests).
- Got: "No failure matched".
- Workaround: `fx-tests crashes`. `failures` apparently does not search crash signatures; saying so in the no-match message would have saved the detour.

## `fx-tests test --bugs` prints nothing about bugs
- Command: `fx-tests test devtools/server/tests/xpcshell/test_nesting-03.js --bugs`
- Expected: a "Bugs" block, or "no bug names this test".
- Got: output identical to the run without `--bugs`, so I could not tell "no bugs" from "flag ignored". (`fx-tests intermittent --test` did say none.)
