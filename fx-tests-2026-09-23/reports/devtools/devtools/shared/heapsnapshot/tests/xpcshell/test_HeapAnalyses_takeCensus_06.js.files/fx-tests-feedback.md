## `fx-tests test <path> --bugs` prints no Bugs section when none is found

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_takeCensus_06.js --bugs`
- Expected: a "Bugs: none" line (or the list).
- Got: the same output as without `--bugs`; no indication whether the lookup ran and found nothing, or failed.

## Android FAIL reported as "Failure details not recorded" when the harness logged why

- Command: `fx-tests test <path>` / `fx-tests task JchzLcinR3uhGM4r4iUK2g --profiles`
- The failure mode was shown as "Failure details not recorded (likely Android or platform logging issue)", but the job's log (resource-usage profile) has `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Surfacing `Failed to start process:` lines as the failure message would have classified it as a harness launch failure without loading the profile.

## Question: "did this test fail on its own, or as part of a job-wide collapse"

- Command: `fx-tests test <path> --task-ids`
- The Windows occurrence was 1 of 1152 TIMEOUTs in its job (msix process launches all failing). `fx-tests test` listed it as an ordinary TIMEOUT; only `fx-tests task` showed "1154 failing". A per-task failing-test count next to each task id in `--task-ids` would flag it immediately.
