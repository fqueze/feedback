## Question: "how did machine memory evolve during this test, in the resource-usage profile?"

- Command: `profiler-cli counter list --session 2016019-1` on
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/DVnIXFj5TI-q8kOtmysnUA/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: a Memory counter, so `counter info c-N` (with `zoom push`) prints the "over time" buckets.
- Got: `No counters in this profile.` Memory is only stored as 24k `Memory` interval markers (100 ms each,
  field `used`). `thread markers --search name:Memory` only shows count and duration stats, not the
  field's values over time.
- Workaround: `zoom push 2100,2395`, then `thread markers --search name:Memory --list --limit 0 --json`
  and a Python script bucketing `data.used` per 10 s. Did this for 4 profiles.
- What would have answered it: a `--group-by time:10s --field used` style summary for marker fields
  (min/max/last per bucket), or `profile info` synthesizing counters from the mozsystemmonitor
  `Memory`/`CPU Use` markers.

## Question: "how many CPU cores did the worker have?" (to compare two jobs' hardware)

- `profiler-cli profile meta` answered it directly (`CPU cores: 4 physical, 8 logical cores`). Good.
  For 350 jobs I read `meta.physicalCPUs` with an HTTP Range request instead of loading each profile.

## Question (review-2016019): "was the machine idle after the OOM crash?" — same gap as above, for `CPU Use`

- Command: `thread markers --search 'name:CPU Use' --list --limit 0 --json --session review-2016019-2` on the NbKA
  resource-usage profile, zoomed to 2298–3400 s.
- Expected: the average/max `cpuPercent` per time bucket, from the default output.
- Got: only counts and duration stats; the values need a script over `--json` (10k markers).
- Would have answered it: the per-bucket field summary requested above, applied to `CPU Use` too.
