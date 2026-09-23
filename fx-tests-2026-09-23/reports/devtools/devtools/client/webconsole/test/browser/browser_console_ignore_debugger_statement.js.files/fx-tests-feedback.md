## Question: which tasks ran this test on this try push, and did any of their manifests leak?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_ignore_debugger_statement.js --task-ids --profiles` (also with `--json`)
- Expected: per config, the task IDs (and resource-usage profile URLs) of the jobs that ran the test, since `--task-ids` / `--profiles` were passed.
- Got: only the per-config pass/fail counts; `--task-ids` and `--profiles` are silently ignored with `--test`, JSON has no task IDs either.
- Workaround: grepped the task IDs of the same config out of other tests' failure rows in `fx-tests try --task-ids` (only works because those jobs failed for other reasons; the passing Windows asan jobs could not be found).

## Question: did this job report a manifest-level leak?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures listed among the job's failures (they are in the resource-usage profile as ERROR markers).
- Got: "2 failing" tests only; the manifest-level LSan failures are absent, so the job looks leak-free.
- Workaround: loaded the resource-usage profile and searched `LeakSanitizer` markers.
