## `fx-tests test <path> --bugs` prints no Bugs section when there is none

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-01.js --bugs`
- Expected: a line such as `Bugs: none found naming this test`.
- Got: the standard summary, with no bug section at all, indistinguishable from the flag being ignored.
- Workaround: took the absence as "none".

## Question: which Android slot (`XpcshellTestRunnerService$iN`) did a test that never launched have?

- Command: `fx-tests task CpX8FW_2TYCexRYJuKooKg --profiles` then profiler-cli + a script replaying `start_test`'s lowest-free-slot rule over the `test` markers and `launch_application` lines.
- What output could have shown: `fx-tests task` knows every test's start/end and (from the launch line) its slot; a per-test "slot" column, or "tests running concurrently when X started", would answer it directly.
- Workaround: `occ.py` in this directory.
