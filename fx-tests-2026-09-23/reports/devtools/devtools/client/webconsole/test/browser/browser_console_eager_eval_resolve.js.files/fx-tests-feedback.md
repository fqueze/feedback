## Question: "the task IDs of the jobs where this test passed on a try push"
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_eager_eval_resolve.js --all-jobs --task-ids --profiles`
- Expected: task IDs (and resource-usage profile URLs) per config for the runs that passed, so I can open one and check the run.
- Got: only the per-config counts table; `--task-ids`/`--profiles` silently ignored. `--json` has no task IDs either.
- Workaround: took the task IDs of the same jobs from another test's row (`browser_console_clear_method.js`, same manifest) in `fx-tests try <rev> --json`.

## Question: "did this job report a manifest-level failure (LSan leak) for the manifest my test is in"
- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --passed --limit 0 --messages --full-messages`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCNativeInterface::NewInstance, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures (9 of them) listed, attributed to the manifest.
- Got: "2 failing" tests only; the manifest-scoped LSan failures are not shown anywhere, nor in `fx-tests try`. A test whose manifest-level asan skip is removed looks clean although its manifest leaked.
- Workaround: loaded the resource-usage profile and searched `LeakSanitizer` markers.
