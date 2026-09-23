## `test --bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_suggestBackendMl.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test (or the list).
- Got: the normal `test` output with no bug section at all, so "no bugs" looks the same as "the flag did nothing".
- Workaround: searched Bugzilla's REST API for the file name.

## Question: which process is this dump from, and how long had it been up?

- Command: `fx-tests crash LEvE_LI7R_OKi-V60yZP_A.0 C87FB148-8A76-4E15-90E8-D660D120A5DB` (signature "child process hang at shutdown")
- Needed: the process type (here the main module is "Firefox Nightly GPU Helper") and `process_uptime`, to tell that the hung child was a GPU process launched at the very start of the test.
- Got: neither in the default or `--all-threads` output; had to read `--raw` JSON (`modules[main_module].filename`, `process_uptime`).
- Could show: a `Process: GPU (Firefox Nightly GPU Helper), up 32 s` line in the header.

## Question: is this failing job's worker one that fails often?

- Needed: the worker of each failing task (55 failing jobs for this test ran on 16 of the pool's 77 workers).
- Got: nothing in `test --task-ids`; had to query the Taskcluster queue API per task.
- Could show: the workerId next to each task id in `test --task-ids`, or a per-worker count.
