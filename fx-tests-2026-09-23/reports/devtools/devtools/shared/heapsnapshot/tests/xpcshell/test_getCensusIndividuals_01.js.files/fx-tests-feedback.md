## Question: why did this Android xpcshell run fail?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_getCensusIndividuals_01.js` (Issues) and `fx-tests task C4lm4orsTaeJMmsNxAKyWg --profiles`
- Expected: the failure message of the Android runs.
- Got: `FAIL Failure details not recorded (likely Android or platform logging issue)` in `test`, and no message at all in `task`.
- The resource-usage profile of the job holds the answer as an INFO line from the harness: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Surfacing the last `remotexpcshelltests.py | Failed to start process:` / `Timing out` / `Could not read log file` INFO line for the test when no TEST-UNEXPECTED line exists would have answered it without loading the profile.
- Workaround: loaded each job's resource-usage profile and searched markers for the test name.

## Question: which tests and jobs does the harness message "Could not kill left-over process" hit?

- Command: `fx-tests errors --harness xpcshell --message "Could not kill left-over process"` and `fx-tests failures --harness xpcshell --message "Failure details not recorded"`
- Expected: the tests (and ideally jobs) whose failure is this harness message, to size a harness-level failure mode.
- Got: `No markers matched` (errors only holds C++/JS warnings and errors, not harness INFO lines), and `No failure matched` (the "Failure details not recorded" label is synthetic, not searchable).
- Workaround: counted it per job by hand in three resource-usage profiles (3-5 tests per affected job); tree-wide count unknown.
