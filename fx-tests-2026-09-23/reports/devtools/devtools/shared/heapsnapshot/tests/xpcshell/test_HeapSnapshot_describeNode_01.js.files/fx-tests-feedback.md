## `test --bugs` is silent when no bug matches

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_describeNode_01.js --bugs`
- Expected: a line such as `Bugs: none found naming this test`.
- Got: the same output as without `--bugs`, with no bug section at all. So "no bug" looks the same as "the flag did nothing".
- Workaround: `curl -s "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"`.

## Question: "what did this Android FAIL actually log?"

- Command: `fx-tests test <path>` shows `2x FAIL Failure details not recorded (likely Android or platform logging issue)`.
- These 2 runs are two different harness mechanisms: `Failed to start process: … | 0 | Could not kill left-over process`, and `Failed wait for remote log: … missing?` + `Could not read log file`. Both lines are in the job's resource-usage profile, as the test's INFO/WARNING markers.
- What it could have shown: for FAILs without details, the test's own `remotexpcshelltests.py | <test> | …` lines from the resource-usage profile, grouped by that message. That would split the Issues row by mechanism.
- Workaround: load each resource-usage profile and `thread markers --search <test file name> --list`.
