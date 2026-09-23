## "Failure details not recorded" when the job's log had them

Question: why did this Android FAIL with no message fail?
Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_02.js` and `fx-tests task UTfxMnRjTQukP_jTsQCU4A`
Expected: the reason, which the job's resource-usage profile holds as an INFO marker naming the test:
`remotexpcshelltests.py | Failed to start process: devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_02.js | 0 | Could not kill left-over process`
Got: `Failure details not recorded (likely Android or platform logging issue)` in `test`, and a bare `FAIL — 1 failing execution of 2` in `task`.
Workaround: loaded the resource-usage profile and searched markers for the test name. The same INFO line explains 5 of the 7 failures in that job (test_ext_scripting_contentScripts.js, test_WebExtensionContentScript.js, test_ext_webRequest_style_cache.js, test_gfxBlocklist_Equal_OK.js too), so attaching it would also group them.

## A failure that was one of 1,152 in its job

Question: is this timeout the test's, or the job's?
Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_02.js --task-ids`
Expected: a flag on the task row that the job was a mass failure (PyUxuOpdQj2b3T3XyQOKpg: 1,152 TIMEOUT, 45 PASS of 1,552 tests), since such a run says nothing about the test.
Got: `TIMEOUT Test exceeded time limit`, counted as the config's 0.5% failure rate, with nothing distinguishing it from a real timeout.
Workaround: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg`, whose header shows the counts.

