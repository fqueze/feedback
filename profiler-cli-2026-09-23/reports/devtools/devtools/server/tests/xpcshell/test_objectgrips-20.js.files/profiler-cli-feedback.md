## "When exactly did this marker start, and how many `test` markers of each status start in a window?"

- Command: `profiler-cli thread markers --session <s> --search test_objectgrips-20 --list --limit 0`
- Expected: start times precise enough to order tests that start 1-4 ms apart, and a per-status count of `test` markers in a time window.
- Got: `t=2m13s` for every row (rounded to the second past 1 min), so neighbouring tests, the first launch failure and the "will retry" line cannot be ordered from the text output; no grouping by a payload field in list mode that I could use for status counts within a zoom.
- Workaround: `--json` and a Python script over `flatMarkers[].start` / `data.status`. (`--group-by field:status` after `zoom push` may cover the count half; the timestamp precision is the real gap.)
