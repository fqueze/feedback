## test_census_filtering_03.js (2026-09-22)

- **Question: what was the failure message of these Android FAILs?**
  `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_filtering_03.js` reports
  `4x FAIL Failure details not recorded (likely Android or platform logging issue)`.
  Expected: the harness line that caused it. The job log (resource-usage profile, TestStatus INFO
  markers) has it every time: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could
  not kill left-over process` (3 runs) or `Failed wait for remote log: ... missing?` + `Could not read
  log file` (1 run). Workaround: load each resource-usage profile and search the test name.
  Surfacing the last harness INFO/WARNING line for the test before `will retry` would have answered it.
- **Question: which other tests fail with this same harness message, tree-wide?**
  `fx-tests errors --harness xpcshell --message "Could not kill left-over process"` → no markers
  (errors file holds test output only); `fx-tests failures --harness xpcshell --message "Failure
  details not recorded"` → "No failure matched", although `fx-tests test` prints that exact text as
  an issue. Expected: the tests behind that issue text. Workaround: none; counted only the ones in
  the jobs I read (4 tests, 5 failures in 3 jobs).
