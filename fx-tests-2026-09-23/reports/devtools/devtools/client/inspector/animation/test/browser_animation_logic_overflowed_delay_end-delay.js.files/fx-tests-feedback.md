## Question: which task ran this test and passed, so I can open its resource-usage profile?

- Command: `fx-tests try 2888bcab0070 --test devtools/client/inspector/animation/test/browser_animation_logic_overflowed_delay_end-delay.js --all-jobs --task-ids --profiles` (also with `--json`)
- Expected: for a test that passed everywhere after its skip-if was removed, the task IDs (or resource-usage profile URLs) of the jobs that ran it, per config, so the PASS can be checked in a profile.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has no task IDs either. `fx-tests task <id>` lists failures only, so it cannot confirm a pass either.
- Workaround: Treeherder API (`/api/project/try/push/?revision=...`, then `/api/jobs/?push_id=...`) to get task IDs for the debug config, then load `profile_resource-usage.json` and search the `test` marker.
- Could have shown: a task ID (and resource-usage URL) column per config row under `--test`.

## Job counts differ between `--test` with and without `--all-jobs`

- Command: `fx-tests try 2888bcab0070 --test <path>` vs the same with `--all-jobs`
- Expected: the same `jobs` count per config.
- Got: without `--all-jobs`, linux asan-11 and linux debug-7 show 4 jobs; with it, 3. The fourth is a Treeherder `retry` run (e.g. WMXHBGdoQGqycQp0UlLS5g run 0). Also `test-windows11-64-25h2-asan/opt-mochitest-devtools-chrome-12` only appears with `--all-jobs`.
- Workaround: none needed; noted in case the retry run is counted as a job in one mode and not the other.
