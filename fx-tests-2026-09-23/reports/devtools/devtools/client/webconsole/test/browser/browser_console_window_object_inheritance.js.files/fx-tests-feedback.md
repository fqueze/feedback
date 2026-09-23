## The task IDs of the jobs that ran (and passed) one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_window_object_inheritance.js --task-ids --profiles --limit 0` (and the same with `--json`)
- Expected: per config, the task IDs (and resource-usage profile URLs) of the jobs that ran the test, so their timelines can be opened.
- Got: only per-config counts (jobs / passed / passed on retry / failed); `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: ran `fx-tests try <rev> --task-ids --json`, and pulled the task IDs of the same configs from a sibling test of the same manifest that failed in those jobs (`browser_console_clear_method.js`). That only works because a sibling happened to fail in every job.

## Did the manifest's shutdown leak check fail in this job?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles` (linux asan chrome-2), and `fx-tests try 2888bcab0070... --profiles --task-ids --limit 0`.
- Expected: the manifest-level `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCWrappedNative::GetNewOrUsed, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures, which the job's resource-usage profile holds (as `LSan Leak` markers and `ERROR` markers scoped to the manifest).
- Got: `task` lists only 2 failing tests (browser_application_panel_open-links.js, browser_console_clear_method.js); `try` never mentions LeakSanitizer at all. A manifest-scoped leak (LSan, or debug `leakcheck`) is exactly what a manifest-wide `skip-if = asan # frequent leaks` guards, so a try push removing it reads as clean when it is not.
- Workaround: loaded each linux asan job's resource-usage profile and searched `LeakSanitizer`.
