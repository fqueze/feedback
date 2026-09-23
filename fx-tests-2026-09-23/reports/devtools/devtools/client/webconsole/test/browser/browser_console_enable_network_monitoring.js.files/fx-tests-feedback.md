## Question: did the manifest holding this test fail at manifest level (LSan leak) in the jobs that ran it?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_enable_network_monitoring.js --all-jobs --task-ids --profiles --limit 0`, then `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: the manifest-level `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures in that job listed (they are the reason the manifest has `skip-if = ["asan"]`, which the push removed).
- Got: `--test` shows 3/3 passed on linux asan; `fx-tests task` lists only the 2 failing tests. The 9 LeakSanitizer ERROR markers attributed to `_browser_console.toml` were only found by loading the job's resource-usage profile in profiler-cli.
- Workaround: load each asan job's resource-usage profile and search `LeakSanitizer`.

## Question: task IDs of the jobs that ran this test (passing ones included)

- Command: `fx-tests try <rev> --test <path> --all-jobs --task-ids --profiles --limit 0`
- Expected: task IDs (and resource-usage profile URLs) per configuration, since `--task-ids` / `--profiles` were passed.
- Got: only the per-config counts table; no task IDs or profile URLs. I had to dig them out of the default `fx-tests try --task-ids` output for other tests' failures in the same chunk, which truncates them (`… 37 more tasks`), so the Windows asan chunk-2 task IDs could not be found.
- Workaround: none cheap (Treeherder job id -> task id lookup).
