## Question: "why did the harness fail this Android xpcshell test with no failure line?"

- Command: `fx-tests test devtools/server/tracer/tests/xpcshell/test_tracer.js` and `fx-tests task SweDwx73QLiNktslJSJpRg --profiles`
- Expected: the harness's own reason for the FAIL of that execution.
- Got: `Failure details not recorded (likely Android or platform logging issue)` for all 4 Android FAILs, and a bare `FAIL — 1 failing execution of 2` row in `task`. The reason is an INFO line the remote harness logs in the job's structured log, `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`: the test never started. It is only visible by loading the resource-usage profile.
- Workaround: `profiler-cli thread markers --search <test file> --list` on the resource-usage profile.
- Suggestion: when a failing xpcshell execution has no failure line, attach the harness's `Failed to start process: ...` / `Could not kill left-over process` INFO line for that test as its message. Grouping on it would also show this is one harness failure mode across many Android tests (5 tests in YzrKWwq7 alone), not a per-test problem.
