## Question: "which job ran this test and passed, so I can read its resource-usage profile next to a failing one?"

- Command: `fx-tests test image/test/mochitest/test_cache_eviction.html --task-ids --limit 0 --config android-em-14-x86_64-ccov`
- Expected: a way to get task IDs of passing runs on a config (43 passes exist on Android ccov, with durations shown by `--durations`).
- Got: only failing task IDs. `--durations` gives pass durations but not the jobs behind them.
- Workaround: Treeherder jobs API for a failing push, then `fx-tests task <id> --passed --limit 0 | rg <test>` over 20 successful ccov chunks: none of them contained the test (chunking moves the manifest between chunks). Gave up; used `--durations` alone.
- Could show: `--task-ids --passing` (or task IDs next to the min/median/max rows of `--durations`).

## Minor: one job listed under two days

- Command: `fx-tests test image/test/mochitest/test_cache_eviction.html --task-ids --limit 0`
- `VfHWTdK-SM-WKLBkvF71vA.0` is listed under both 2026-09-08 and 2026-09-09, and the header says "1 of 51 failing jobs saw the failure more than once, which within a job is a harness rerun". Android mochitest has no rerun here (the job shows 1 execution), so this looks like one job spanning midnight counted twice, and the rerun wording misleads.
