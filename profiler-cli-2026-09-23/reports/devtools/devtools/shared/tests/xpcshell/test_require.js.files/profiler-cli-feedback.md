## Bare-term exclusions in `thread markers --search` drop every marker

- Command: `profiler-cli zoom push 124.9,127.5 --session S; profiler-cli thread markers --session S --search '-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:SKIP,-will retry' --list`
- Expected: the INFO `Retrying tests that failed when run in parallel.` and the ERROR traceback in that range (neither contains "will retry").
- Got: `0 markers (filtered from 105)`. Same with `-Begin of full,-End of full,-return code,-not killing`. With only `-name:` exclusions the same kind of query worked; switching to positive `name:INFO,name:ERROR,...` found the 18 markers.
- Workaround: positive `name:` terms instead of bare-term exclusions.

## Question: "when did a job-wide failure start, and how many tests ended the same way"

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json` then a Python script bucketing `test` markers by start time, status and duration.
- The default output has per-name duration stats, but not a breakdown of the `test` markers by status over time; a `--group-by field:status` with time buckets (or a histogram of start times per status) would have answered "1057 TIMEOUTs of ~45 s, all started within t=45..55s" directly.
