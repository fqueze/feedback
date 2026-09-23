## Question: "why did this Android xpcshell run fail?" (Issues says only "Failure details not recorded")

- Command: `fx-tests test devtools/shared/network-observer/test/xpcshell/test_network_helper.js`
- Got: `1x FAIL Failure details not recorded (likely Android or platform logging issue)`.
- The answer was an INFO marker in the job's resource-usage profile: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Same gap as logged for `test_security-info-weakness-reasons.js`; showing that line would make the diagnosis and group the tests failing this way across jobs.
