## `--bugs` prints nothing when no bug names the test
- Command: `fx-tests test devtools/platform/tests/xpcshell/test_nativewrappers.js --bugs`
- Expected: a line saying no bug names this test (or lists the bugs).
- Got: output identical to the run without `--bugs`, with no Bugs section at all. It reads as if the flag had been ignored. `--json` does show `"annotatedBugs": []`.
- Workaround: `--json` and read `annotatedBugs`.

## Android failures show "Failure details not recorded" although the job profile has the reason
- Question: why did this Android xpcshell test fail when its Issues row is `Failure details not recorded (likely Android or platform logging issue)`?
- Command: `fx-tests test devtools/platform/tests/xpcshell/test_nativewrappers.js`
- The reason was in each job's resource-usage profile, as the harness INFO/WARNING line for the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process` in one job, and `Failed wait for remote log ... missing?` / `Could not read log file` in the other. Those lines were 5 of 8 and 8 of 14 of the jobs' failures.
- The Issues row (or `fx-tests task`) could have shown that harness line instead of the placeholder. Getting it meant loading both profiles.
