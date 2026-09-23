## Question: "what did the harness say about this failed Android run?"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-10.js` and `fx-tests task JE8wwSLFSkOzb42PUOhI5w --profiles`
- Expected: the harness's own lines for the failing execution: here `Failed wait for remote log: … missing?` and `Could not read log file: … No such file or directory`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`. The lines were in the job's resource-usage profile all along, as WARNING/INFO markers naming the test.
- Workaround: load the resource-usage profile and search it for the test name. `fx-tests task` could show, per failing test, the WARNING/INFO markers that name it, which also tells "never launched" (`Could not kill left-over process`) from "launched, log never created" at a glance.

## `--bugs` prints nothing when it finds nothing

- Command: `fx-tests test <path> --bugs`
- Expected: a line such as `Bugs: none found`.
- Got: the normal output with no bugs section at all, so "searched, none" and "flag ignored" look the same.
