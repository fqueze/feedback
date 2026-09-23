## Question: which task IDs ran this test (and passed) on a try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_content_object_in_sidebar.js --task-ids` (also with `--json`)
- Expected: the task IDs behind each per-config row (jobs/passed/failed), since `--task-ids` was passed.
- Got: only the per-config counts; `--task-ids` silently ignored, and the JSON has no task IDs either.
- Workaround: `fx-tests try <rev> --task-ids --limit 0 --json` and a script walking the JSON for `jobName`/`taskId` of the config; only works because those jobs failed for other tests. For a config where all jobs were green there would be no way.
- Also: in the text output of `fx-tests try --task-ids --limit 0`, each row's task list still ends with `… 37 more tasks` — `--limit 0` does not expand it.

## Question: did the failure the skip-if exists for (a shutdown LeakSanitizer leak) happen in this job?

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles` (and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88`)
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCNativeInterface::NewInstance, ... | devtools/client/webconsole/test/browser/_browser_console.toml` listed among the job's failures (it is what `skip-if = ["asan"]` on that manifest's DEFAULT is for, Bug 1910261).
- Got: "FAILED (2)" listing only the two per-test failures; the manifest-level LSan failure is absent from both `task` and `try`, so a reader concludes the asan skip is unneeded.
- Workaround: loaded each job's resource-usage profile and searched markers for `LeakSanitizer`.
