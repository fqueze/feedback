## `fx-tests test` hides the Android harness message

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_02.js` (and `fx-tests task GykA21OhS3GgJSiLBxt_kA --profiles`)
- Expected: the failure message of the Android run.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)`, and in `task`, no message at all for this test. The job's resource-usage profile has it as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Workaround: load the resource-usage profile and search markers for the test name.

## Question: "which service slot did this Android test run on?"

- Needed to tell a harness slot collision from a device-side one. It took a script over `profiler-cli thread markers --search "launch_application,name:test" --list --limit 0 --json`, matching `Service$iN` in launch lines to test markers, and inferring by elimination the slot of a test that never launched.
- `fx-tests task` could show the slot next to each Android test (from the `$iN` of its launch, or "none launched").
