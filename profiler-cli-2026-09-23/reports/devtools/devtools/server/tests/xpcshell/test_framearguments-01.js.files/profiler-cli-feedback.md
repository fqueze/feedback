## Question: "exact start time of a marker, and the gap between two markers"
- Command: `profiler-cli thread markers --search test_framearguments-01 --list --limit 0 --session …`
- Expected: millisecond start times, such as 133.148s.
- Got: `t=2m13s` for every marker past one minute. The 36 ms gap between the `test` start and "will retry" cannot be seen, and `marker info` also prints `2m13s - 2m58s`.
- Workaround: `--json` with `flatMarkers[].start`.

## Question: "how many tests started after time T, and with what status?" (resource-usage profile)
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python script that counts by `data.status` before and after T.
- Expected output that could answer it: `--group-by field:status` over a zoom range. Zooming does not help here, because interval markers that only overlap the range get included.
- Also: in `--json`, `flatMarkers[]` has no `duration` key for instant markers, so `m['duration']` raised a KeyError. It could be `null` instead.
