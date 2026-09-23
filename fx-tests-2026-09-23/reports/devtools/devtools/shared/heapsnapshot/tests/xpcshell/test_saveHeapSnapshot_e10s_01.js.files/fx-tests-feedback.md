## Question: "what did the harness say when it failed this test?"

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_saveHeapSnapshot_e10s_01.js` and `fx-tests task C-J5ZVIiT6KtZN-QFxuxPg --profiles`
- Expected: the harness's own reason for the FAIL, or at least a pointer to where it is (the resource-usage profile's replayed log).
- Got: `Failure details not recorded (likely Android or platform logging issue)` for 12 failures that are all on Windows, and a `FAIL — 1 failing execution of 2` row with no message and no profile in `task --profiles`. The "likely Android" hint is wrong here and sends you looking in the wrong place. The reason was only found in the resource-usage profile: the xpcshell harness never parsed `CHILD-TEST-COMPLETED` (glued to another JSON line), so it failed the test without any failure line.
- Workaround: loaded the resource-usage profile and read the test's replayed log (`thread markers --search <test> --list`).
- Suggestion: when an xpcshell FAIL has no message, say "no failure line logged; the harness failed it on exit status or missing CHILD-TEST-COMPLETED; read the replayed log in the resource-usage profile" instead of the Android hint.
