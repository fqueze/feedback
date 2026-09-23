## Question needing a script: the Android FAIL's real message, and which slot the test held

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_09.js` and `fx-tests task GL-dWnKoQ8Wp2wJRiHyKuA --profiles`
- Expected: the failure's message. The job's resource-usage profile has it as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`.
- Workaround: loaded the resource-usage profile and searched for the test name.
- Related question that needed a script over `--json`: "which slot (`XpcshellTestRunnerService$iN`) did each test use, and which slot did the failing test (never launched) hold". `fx-tests task` on an Android job could list each test's slot from its `launch_application` marker, and flag `Could not kill left-over process` failures with the slot name.

## `test --task-ids` reprints the whole summary

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the task IDs (I had just run `fx-tests test <path>`).
- Got: the full summary again, then the task IDs. Minor token cost.
