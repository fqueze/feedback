## Question: why did 1152 tests of this job all TIMEOUT at once?

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: for a job where 1152 of 1552 tests TIMEOUT within the same few seconds, a line surfacing the harness's own error, which is the answer: the `Following exceptions were raised:` traceback at the end of the log (`PermissionError: [WinError 5] Access is denied` from `CreateProcess` in `launchProcess`), or at least a note that most TIMEOUTs share one start/end time.
- Got: 1154 entries listed one by one, each "TIMEOUT — Test timed out", and nothing about the traceback.
- Workaround: loaded the resource-usage profile and searched its markers for `Traceback` / `Following exceptions`.

## `fx-tests test <path> --bugs` prints no bug section when none is found

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_03.js --bugs`
- Expected: a "Bugs: none naming this test" line, so it reads as searched and found nothing.
- Got: the same output as without `--bugs`, so I could not tell whether the flag did anything.
- Workaround: a Bugzilla quicksearch on the error text found bug 1991833.
