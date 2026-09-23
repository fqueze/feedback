## Question: "was this test's only failure part of a job-wide breakdown?"
- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_client_server_bulk.js`
- Expected: some sign that the single TIMEOUT came from a job where 1,375 of 1,854 tests failed (the MSIX package stopped launching).
- Got: "Verdict: intermittent ... TIMEOUT Test exceeded time limit", which reads like a real intermittent. I only found out with `fx-tests task XfKT04wZR42kSNwV6wuRpQ`.
- Could show: a flag or count on failures coming from jobs with a mass failure (e.g. more than N% of the job's tests failing), or exclude them from the verdict.

## Question: "which jobs hit Gecko's child-launch error 0x80073CFC?"
- Command: `fx-tests errors --harness xpcshell --day 2026-09-10 --message "-2147009284"` (also tried `--message "0x80073cfc"`)
- Expected: the jobs that logged `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)`.
- Got: "No markers matched." Messages are stored normalized (`Error:-<num>`), so the error code, which is what tells these apart, can't be searched and doesn't appear in the output.
- Workaround: `--message "Failed to launch"`, which mixes every launch error code together.

## `fx-tests test <path> --bugs` with no matching bug prints nothing
- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_client_server_bulk.js --bugs`
- Expected: a "no bug names this test" line.
- Got: output identical to the run without `--bugs`, so I couldn't tell whether the flag was applied.
