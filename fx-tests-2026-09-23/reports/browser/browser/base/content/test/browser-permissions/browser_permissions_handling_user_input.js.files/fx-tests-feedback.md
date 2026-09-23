## When did each test of a job run, and in what order? (browser_permissions_handling_user_input.js)

- Command: `fx-tests task fJTT8g8mSeOW9Zn3haOAog --passed` (and `--json`)
- Expected: per-test start time / order in the job, so I could tell whether the failing test ran in the job's first minute (the failure correlates with a disk write burst ~40-90 s into Linux jobs).
- Got: outcomes only; the JSON `passed`/`failures` rows carry no start time or position.
- Workaround: loaded each job's resource-usage profile in profiler-cli and scripted over `thread markers --search name:test --json` to sort `test` markers by start. Five loads for one question.
- What would have answered it: a start time (seconds into the job) column per test, or `--order`.

## Was the machine's disk saturated while this test ran?

- Command: none in fx-tests; the resource-usage profile has `IO` markers (write bytes per ~100 ms).
- Expected: something like `fx-tests task <id> --around <test>` summarising CPU %, iowait % and disk write MB/s during a given test's run.
- Got: nothing; needed profiler-cli + a script bucketing `IO` and `CPU Use` markers per second.
- Note: in 6 Linux jobs checked, a 16-18 s burst of writes at ~168 MB/s with 30-50% iowait happens 40-90 s into the job; that is invisible from fx-tests today.
