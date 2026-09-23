## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_fetch-resource.js --bugs`
- Expected: a line saying no open or closed bug names this test (and ideally which search was run).
- Got: exactly the same output as without `--bugs`, so "no bug" and "flag ignored" look identical.
- Workaround: searched Bugzilla by hand.

## Android xpcshell failure message shown as "Failure details not recorded"

- Question: what message did the 2 Android FAILs of this test have?
- Commands: `fx-tests test <path>` (Issues: `FAIL Failure details not recorded (likely Android or platform logging issue)`), `fx-tests task bLu6Vou-TX-lfTVzsIg8JA.0 --profiles` (`FAIL — 1 failing execution of 2`, no message).
- The job's resource-usage profile has the answer as an INFO line on the mach thread: `remotexpcshelltests.py | Failed to start process: devtools/shared/tests/xpcshell/test_fetch-resource.js | 0 | Could not kill left-over process`, followed by `… failed or timed out, will retry.`
- Could show: the harness's `Failed to start process: …` line as the failure message when the test produced no TEST-UNEXPECTED line. Same for the other tests failing that way in the same jobs.
