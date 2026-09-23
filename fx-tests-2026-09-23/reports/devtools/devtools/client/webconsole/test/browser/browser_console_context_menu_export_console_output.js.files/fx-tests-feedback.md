## Manifest-level LeakSanitizer failures are invisible

- Question: did this test's manifest leak at shutdown in this job? (The `skip-if = asan` being removed was for exactly that, Bug 1910261.)
- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg` (also `fx-tests try 2888bcab0070 --task-ids --limit 0`, and `fx-tests task ... --json`)
- Expected: a row for `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml`, which the job's resource-usage profile holds (10 such ERROR markers at t=23m23s).
- Got: "2 failing", both other tests; the word LeakSanitizer appears nowhere in text or JSON output, and `try` does not list it either.
- Workaround: load the resource-usage profile and `thread markers --search LeakSanitizer`.

## Task IDs of the jobs that ran one test on one config

- Question: which tasks ran this test on test-windows11-64-25h2-asan/opt-mochitest-devtools-chrome-2?
- Command: `fx-tests try <rev> --all-jobs --test <path> --task-ids` prints the per-config table with no task IDs (none in `--json` either). `fx-tests try <rev> --task-ids --limit 0` still cuts per-row task lists with "… N more tasks" (24 times), and `--config` is refused on `try`.
- Workaround: walked `fx-tests try <rev> --task-ids --limit 0 --json` with a script for `taskId` + `jobName`.
- Could show: task IDs per config row in `--test` output.
