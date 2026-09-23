## Mean of a marker payload field over a zoomed range

Question: "what was the machine's average CPU use while this test ran?" (resource-usage profile, `CPU Use` markers, `cpuPercent` field).

- Command: `profiler-cli zoom push m-58 --session S; profiler-cli thread markers --session S --search "name:CPU Use"`
- Expected: some aggregate of the numeric payload fields (mean/median/p95 of `cpuPercent`, `idle_pct`) over the view, since the aggregate view already gives duration stats.
- Got: only count and duration stats; the numeric payload is only in `--list --limit 0 --json` (`data.cpuPercent` as a "87.7%" string).
- Workaround: a Python script over `--list --limit 0 --json` parsing the percent strings.

## When did this test's device process exit? (review-test_HeapAnalyses_getImmediatelyDominated_01.js)

Question: "in an Android resource-usage profile, between which two `get_process_list` snapshots does process `org.mozilla.geckoview.test_runner:xpcshell1` (or pid N) disappear?" This is how you tell a hung test from a slow one, because the harness's `Application ran for` is only a poll time.

- Command: `profiler-cli thread markers --session S --search get_process_list --list --limit 0`
- Expected: some way to follow one process name or pid across these markers, e.g. `--search 'message:xpcshell1'` giving present/absent per snapshot, or a derived "process lifetime" track.
- Got: each marker is one huge list literal, truncated in the text output. Every snapshot matches the search, whether the process is in it or not.
- Workaround: a Python regex over `--list --limit 0 --json`, run once per profile.
