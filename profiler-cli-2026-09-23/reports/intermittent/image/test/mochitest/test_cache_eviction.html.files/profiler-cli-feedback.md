## Question: "what was the machine's CPU use during test X, against the rest of the job?" (resource-usage profile)

- Command: `profiler-cli zoom push m-48 --session <s>; profiler-cli thread markers --search "name:CPU Use" --session <s>`
- Expected: the average (and ideally the shape over time) of `cpuPercent` within the zoom, since resource-usage profiles carry CPU only as `CPU Use` markers and have no counters (`counter list` says "No counters in this profile", `profile info` says "No significant activity").
- Got: marker count and interval-duration stats only; `--group-by field:cpuPercent` gives a histogram of exact string values (87.5%: 473 markers, ...), not a mean.
- Workaround: `--list --limit 0 --json` for all `CPU Use` and `test` markers, then a Python script averaging `cpuPercent` per `test` marker and per 20 s bucket.
- Could show: numeric field stats (min/mean/max) per marker type in the aggregate view, or a per-test CPU column for `test` markers in resource-usage profiles.

## Question: "was the test still making progress when it timed out?" (resource-usage profile, review-test_cache_eviction.html)

- Command: `profiler-cli zoom push m-48 --session <s>; profiler-cli thread markers --search name:NetIO --session <s>`
- Expected: bytes received over the zoom, and per time bucket. Each ~130 KB burst here is one image load of the test, so that would count its iterations.
- Got: marker count and interval-duration stats only. Same gap as the `CPU Use` entry above, for `NetIO`, `IO` and `Memory`.
- Workaround: `--list --limit 0 --json` for all `NetIO` markers (14 MB), then Python bucketing and counting samples of 110–175 KB.
- Could show: expose these resource-usage marker fields as counters, so `counter info` gives the "over time" buckets it already prints for counters.
- Minor: in `--list --json`, instant markers have no `duration` key at all, while interval ones do. A script that reads `m['duration']` fails with a KeyError on the first instant marker.
