## Android harness failure shown as "Failure details not recorded" (test_HeapSnapshot_computeShortestPaths_02.js)

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_computeShortestPaths_02.js` and `fx-tests task cjjCz4aASie9OWw2OJ8-sw --profiles`
- Expected: the failure message of the Android run.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` in `test`, and `FAIL — 1 failing execution of 2` with no message in `task`.
- The job's resource-usage profile holds the reason, right at the test's end: an INFO marker
  `remotexpcshelltests.py | Failed to start process: <test path> | 0 | Could not kill left-over process`,
  followed by `<test path> failed or timed out, will retry.` The same job had two more tests failing
  exactly that way (test_ext_wasm.js, test_ext_web_accessible_resources.js).
- Workaround: load the resource-usage profile and `thread markers --search <test file> --list`.
- Also missing: a way to count one harness message ("Could not kill left-over process") across the tree.
  `fx-tests errors --harness xpcshell --message ...` finds nothing (it only has failing tests' replayed
  stdout), and `fx-tests failures --message` only has the per-test messages.
