# profiler-cli feedback — test_timer_flood.html

## Question: how busy was the whole machine between t1 and t2 (resource-usage profile)?

- Command: `profiler-cli counter list --session test_timer_flood.html-1` on
  `dgAYedo7QCqEi55_NKj7Jw/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: a CPU counter I can `counter info` under a zoom, as the brief suggests ("the CPU use of
  the whole machine").
- Got: `No counters in this profile.` The CPU data are 28,033 `CPU Use` interval markers
  (one per 100 ms, `cpuPercent` field). `thread markers --search "name:CPU Use"` under a zoom only
  gives counts and durations, not the mean/max of `cpuPercent`.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script
  averaging `data.cpuPercent` per 30 s bucket.
- What could have answered: field statistics (mean/min/max) for numeric payload fields in the
  aggregated `thread markers` view, or exposing the resource-usage CPU markers as a counter.

## Question: which threads were busy during this zoomed range?

- Command: `profiler-cli zoom push 150,450 --session test_timer_flood.html-2` then
  `profiler-cli profile info --session test_timer_flood.html-2`
- Expected: per-thread CPU time within the zoomed range.
- Got: the same full-profile CPU totals as without zoom (e.g. `t-0: GeckoMain - 79173.570ms` in
  both), only the counters' memory ranges changed.
- Workaround: `thread samples-top-down --include-idle` per thread to see the idle share.

## Question: the test's own log in a mochitest-plain per-test profile

- Command: `profiler-cli thread markers --session test_timer_flood.html-2 --category Test --search test_suppressed_events_nested_iframe --list --limit 0`
  on the parent process main thread (t-0), as the brief says.
- Expected: the test's TEST-PASS / INFO lines.
- Got: `No markers match`. In this mochitest-plain (xorig) profile the log markers are on the
  harness content process thread (`t-25`, `http://mochi.xorigin-test`) and their text does not
  contain the test file name; only the `test` interval marker does.
- Workaround: `profile markers --search "<a string from the test>"` to find the thread, then
  `thread markers --category Test` on it.

## Minor

- `thread markers --search "name:test"` also matches `TEST-PASS`, `TEST-KNOWN-FAIL`... (substring),
  so there is no way to ask for the `test` interval markers alone except `--min-duration`.
- `marker info m-1 m-2 --json` returns records without a `handle` field, so a multi-handle
  result cannot be matched back to the handles requested except by order.

## Review (review-test_timer_flood.html)

### Question: the timestamps of instant markers matching a search

- Command: `profiler-cli thread markers --session review-test_timer_flood.html-5 --search "one text fragment range,Unable to restore focus,name:test" --min-duration 0 --list --limit 0`
- Expected: `--min-duration 0` to be a no-op, keeping instant markers.
- Got: every instant marker (`PASS`, `INFO`) silently dropped, only interval `test` markers left.
- Workaround: omit `--min-duration`.
