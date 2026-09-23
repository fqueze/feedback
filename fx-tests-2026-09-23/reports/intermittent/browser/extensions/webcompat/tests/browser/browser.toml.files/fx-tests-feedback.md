## Manifest-level leakcheck failures cannot be reached from `fx-tests test` or `fx-tests task`

- Command: `fx-tests test browser/extensions/webcompat/tests/browser/browser.toml`
- Expected: the leakcheck failures attributed to this manifest (`leakcheck large nsGlobalWindowInner | …/browser.toml`), with configs and task ids.
- Got: "No test path in the xpcshell and mochitest 21-day data contains …". Only `fx-tests intermittent --bug 2010783` found the jobs.
- Command: `fx-tests task LiEnGsrrQvuzs5kAvA6WzQ --profiles`
- Got: "No test-level failure in this job … Read the log". The job's errorsummary artifact has the leakcheck line, and the resource-usage profile has `Leaked Total` markers that name the scope and the process.
- Question it could have answered: "which process leaked in this leakcheck failure, and which test was running when that process was created and when it shut down". I answered it with a script over six downloaded live_backing.log files (trace the `[pid = N]` DocShell/DOMWindow lines inside the manifest section, then find the first `ShutdownXPCOM` atom assertion of that pid).
