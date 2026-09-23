## Android failures shown as "Failure details not recorded"
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_filtering_05.js`
- Expected: the failure mode named, for the 2 Android failures.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`.
- Where the answer was: in the job's resource-usage profile, the harness logged `WARNING <test> | Failed wait for remote log: <path> missing?` and then `Could not read log file: ... No such file or directory` for this test. Surfacing that WARNING as the message would name the mode, "the xpcshell process never created its log", and let these be grouped across tests.
- Workaround: load each resource-usage profile and search it for the test name.

## Question: "is this job a job-wide collapse?"
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Got: 1,154 failing tests listed one by one.
- What would have answered it: a one-line verdict. In this job, 1,149 tests started within 4 s (52–56 s), each got "will retry" within milliseconds, and each ended TIMEOUT about 45 s later.
- Workaround: a script over the resource-usage profile's `test` markers (`--json`), bucketing TIMEOUT start times.
