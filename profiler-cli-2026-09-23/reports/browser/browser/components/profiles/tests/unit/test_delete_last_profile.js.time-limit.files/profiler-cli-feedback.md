## Question: "at what millisecond did each of this test's log lines happen" (23-minute resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search test_delete_last_profile --list --limit 0`
- Expected: start times precise enough to order the test's lines against each other and against the kill (they are 1–100 ms apart).
- Got: `t=4m59s`, `t=5m` for every line between 298.6 s and 300.1 s, so "exiting test" (299.820 s) and the static-dtor warnings (300.091 s) cannot be told apart.
- Workaround: `--json` and a script printing `start`. The list could print milliseconds (or seconds with 3 decimals) when the profile is long, or when zoomed to a short range.

## Question: "was the machine saturated during this 90 s window" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 296,392`)
- Expected: one number per second or per bucket for the window.
- Got: hundreds of `CPU Use` rows with no value column in the list output.
- Workaround: `--json`, then a script averaging `data.cpuPercent` weighted by duration per 5 s bucket. A `--group-by` time bucket with the mean of a numeric field, or `CPU Use` exposed as a counter so `counter info` does it, would answer it.
