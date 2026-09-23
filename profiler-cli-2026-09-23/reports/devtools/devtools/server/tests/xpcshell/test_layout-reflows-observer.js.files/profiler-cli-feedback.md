## Question: "exactly when did this test start, and how long after it did the harness give up?"
- Command: `profiler-cli thread markers --session <s> --search test_layout-reflows-observer --list --limit 0`
- Expected: start times precise enough to subtract (ms), since instants 35 ms apart matter here.
- Got: `t=2m13s` for both the test marker and the `will retry` INFO; `marker info` also prints `2m13s - 2m58s`.
- Workaround: `--json` and a Python one-liner to read `start`.
- Could have shown: millisecond start times in `--list` (at least when the view spans minutes and rows share a rounded time).

## Question: "how many tests passed / timed out before vs after time T (the first launch failure)?"
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` + script grouping `data.status` by `start < T`.
- Expected: a way to group Test markers by a payload field within a zoom range.
- Got: needed the script; `--group-by field:status` with `zoom push 0,T` might do it, but the Test marker's status field key was not discoverable without reading JSON first.
- Could have shown: per-status counts for Test markers in the default `thread markers` summary.
