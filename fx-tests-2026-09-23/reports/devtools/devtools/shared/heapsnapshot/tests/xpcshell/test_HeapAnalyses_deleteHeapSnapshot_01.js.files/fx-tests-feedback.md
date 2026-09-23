## test_HeapAnalyses_deleteHeapSnapshot_01.js (2026-09-22)

- **Question: what was the failure message of these Android FAILs?**
  `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_deleteHeapSnapshot_01.js`
  → `3x FAIL Failure details not recorded (likely Android or platform logging issue)`, and
  `fx-tests task <id> --profiles` shows no message for the test. The resource-usage profile has
  `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`
  (2 runs) and `Failed wait for remote log: … missing?` (1 run). These are two different failure
  modes merged under one issue. Expected: the harness INFO/WARNING line for the test before
  `will retry`. Workaround: load each profile.
- **`fx-tests test <path> --task-ids` gave `fHprygykTfC90K_1pxICeA.1`; `fx-tests task fHprygykTfC90K_1pxICeA`
  (no run suffix) exits 4 "artifact is not there … permanent"**. Expected: use the run given by
  `--task-ids`, or say that run 0 has no artifact but run 1 does. Workaround: pass `.1`.
