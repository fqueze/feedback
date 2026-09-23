## Question: why did an Android run fail with "Failure details not recorded"?

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_dnr_dynamic_rules.js` (Issues row 1, 470x) and `COLUMNS=250 fx-tests task MCt9GQjQS-uyI4SquaadgA --profiles --messages`
- Expected: some reason for the failure, since these are 42% of the test's failures.
- Got: the Issues row "Failure details not recorded (likely Android or platform logging issue)", and `task --messages` printing no message at all for the failing test.
- Workaround: load the job's resource-usage profile and `thread markers --search "test_ext_dnr_dynamic_rules.js,Failed to start process"`. The harness had logged `remotexpcshelltests.py | Failed to start process: Only one instance of an application may be running at once` 3 s after the test started, at the beginning of the parallel phase (4 of 4 sampled jobs).
- What could have answered it: when a test has no recorded failure message, `task` (and the Issues grouping in `test`) could fall back to the harness lines logged for that test (or right after it started) in the resource-usage profile: "Failed to start process", "failed or timed out, will retry". That would also split the 470 into their real causes.
