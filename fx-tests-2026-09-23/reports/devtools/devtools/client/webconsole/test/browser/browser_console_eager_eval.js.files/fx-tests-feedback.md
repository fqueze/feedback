# fx-tests feedback (browser_console_eager_eval.js)

## Question: "did the manifest-level leak this skip-if guards recur on the try push?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0` and `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: the `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures of task SG5h3qzRTDi60GlyHb25Sg listed, under the manifest (the skip-if being evaluated is `skip-if = ["asan"] # frequent leaks on asan` in the manifest's `[DEFAULT]`).
- Got: no mention anywhere. `fx-tests task` says "189 tests, 2 failing" and lists only two test failures. The resource-usage profile of the same task does hold 9 `ERROR` markers `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | _browser_console.toml` and the `==15935==ERROR: LeakSanitizer: detected memory leaks` output marker, so the data is there.
- Workaround: downloaded all 23 `live_backing.log`s and grepped them for `TEST-UNEXPECTED-FAIL.*_browser_console.toml` and `ERROR: LeakSanitizer`.
- Could have shown: a "manifest-level failures" section (LeakSanitizer, leakcheck, shutdown crashes) per task and per push, attributed to the manifest path. Every skip-if whose reason is "leaks" is otherwise invisible to `fx-tests try`, and would read as `no longer fails`.

## Question: "which tasks ran this test and passed?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_eager_eval.js --task-ids --profiles` (also with `--json`)
- Expected: the task IDs behind each config's `passed` count, since `--task-ids` was passed.
- Got: only the per-config counts table; no task IDs in text or JSON.
- Workaround: `curl https://treeherder.mozilla.org/api/jobs/?push_id=2044083&count=2000` and filtered by job name.
