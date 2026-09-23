## test_DominatorTree_06.js (2026-09-22)

- **Question: "was this failure the test's, or one of hundreds in a broken job?"**
  `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_06.js` lists the msix
  run as `TIMEOUT Test exceeded time limit`, the same way it lists a real timeout. Only
  `fx-tests task PyUxuOpdQj2b3T3XyQOKpg` showed `1154 failing` (1152 TIMEOUT). Expected: `test --task-ids`
  (or the Issues list) flags runs from jobs where most tests failed, e.g. "1 of 1152 failures in this
  job", and ideally names the sheriff bug the job was starred with (bug 1991833 here). Workaround: run
  `fx-tests task` on every task id.
- **Question: "why did the Android run fail?"** `fx-tests test` says `FAIL  Failure details not recorded
  (likely Android or platform logging issue)`. The resource-usage profile has the answer as an INFO
  marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
  Expected: when there is no failure message, show the harness's `Failed to start process` /
  `will retry` INFO line for that test. Workaround: load the resource-usage profile and search it.
- **Question: "how often does `Could not kill left-over process` happen across tests?"**
  `fx-tests errors --harness xpcshell --message "Could not kill left-over process"` gives `No markers
  matched`, because errors files hold only C++/JS error markers, not harness INFO lines. No command
  answers this.
