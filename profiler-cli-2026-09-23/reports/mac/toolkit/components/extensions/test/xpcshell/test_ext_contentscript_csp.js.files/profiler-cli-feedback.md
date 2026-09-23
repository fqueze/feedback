## Question: what was the machine's CPU use during one test's run (mean / min / share at 100%)?

- Command: `profiler-cli zoom push m-1 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json` on the resource-usage profile, then a Python script averaging `data.cpuPercent`.
- Expected: the default (non-JSON) `thread markers --search "name:CPU Use"` aggregate to summarize the numeric field over the view (mean/min/max of CPU Percent), since that is the only reason to look at these markers.
- Got: the aggregate only gives counts and durations; the list prints no field values at all (blank payload column for `CPU Use`).
- Workaround: `--json` plus a script.

## Question (review): how many tests ran at the same time as this one?

- Command: `profiler-cli zoom push m-1 --session <s>` on the resource-usage profile (m-1 = this test's `test` marker), then `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a script counting, every 250 ms, the `test` markers covering that instant.
- Expected: for an interval marker (or a zoomed view), the number of same-name interval markers overlapping it at once (min / median / max), leaving out the harness's phase markers (`parallel`, `retry`, `replaying full log for …`).
- Got: only the list of the 43 `test` markers overlapping the view, which includes the `parallel` phase marker and a log replay. The count of markers overlapping over 73 s is not the concurrency: the report read "about 40 other tests running" from it, when 10 ran at a time on the 4-core VM.
- Workaround: `--json` plus a script.
