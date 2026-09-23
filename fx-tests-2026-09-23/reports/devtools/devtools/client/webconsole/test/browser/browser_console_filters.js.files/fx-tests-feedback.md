## Question: which jobs ran this test on a try push, so I can open their logs/profiles?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_filters.js --all-jobs --task-ids --profiles --limit 0` (also with `--json`)
- Expected: the task IDs (and resource-usage profile URLs) behind each config row, as `--task-ids`/`--profiles` give elsewhere.
- Got: only per-config counts; `--task-ids` and `--profiles` are silently ignored with `--test`, in text and JSON.
- Workaround: grepped task IDs for the config out of the unrelated failures listed by plain `fx-tests try` (only found the Linux ASan ones; the Windows ASan ones were behind "… 37 more tasks").

## Question: did the manifest-level LeakSanitizer failure the skip-if was for come back on the try push?

- Command: `fx-tests try <rev> --profiles --task-ids`, `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles`
- Expected: `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` listed as a failure of that job (it is in the resource-usage profile as `LSan Leak` and `ERROR` markers attributed to the manifest).
- Got: neither command mentions it; `task` says "2 failing" (two unrelated tests) and the per-test `--test` view shows every test of the manifest as passed.
- Workaround: downloaded `live_backing.log` of each Linux ASan job and grepped for `LeakSanitizer`, then found the markers in the resource-usage profile.
- Minor: without `--all-jobs`, `--test` said 4 jobs for `opt-mochitest-devtools-chrome-5` and `-a11y-checks-5`; with `--all-jobs` it said 3.
