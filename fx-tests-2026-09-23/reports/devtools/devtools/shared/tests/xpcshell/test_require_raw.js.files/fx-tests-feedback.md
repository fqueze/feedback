## Question: "which bug are this test's failing jobs starred with?"
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_require_raw.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs star these exact jobs with.
- Got: no bug at all (the output is the plain `test` view). The bug's summary names no test, so a name search cannot find it.
- Workaround: Bugzilla quicksearch on "msix Access is denied", found by guessing from the profile's traceback.
- Could have shown: the bugs that the failing task IDs were annotated with (Treeherder classifications), not only bugs whose summary names the test.

## Question: "did this job fail as a whole (harness exception or infra), rather than this test?"
- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a line saying the job ended on a harness exception (`PermissionError: [WinError 5] Access is denied` from `launchProcess`), or that 1060 of 1472 tests timed out within about 4 s of each other, so it was a job-wide event.
- Got: "1061 failing" with the first 20 listed alphabetically. There is no hint that this was one event, and the traceback does not appear.
- Workaround: loaded the resource-usage profile and bucketed the `test` markers by start time with a script over `--json`.
- Could have shown: a verdict for mass failures (for example "N tests TIMEOUT, starting within X s"), plus the harness's final ERROR/traceback.
