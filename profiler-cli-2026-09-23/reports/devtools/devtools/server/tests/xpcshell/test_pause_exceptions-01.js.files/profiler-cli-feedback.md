## Marker times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --search test_pause_exceptions-01 --list --limit 0 --session test_pause_exceptions-01.js-2` (resource-usage profile of LKys8j8iTQKTAN9hKv040g, 3m37s long)
- Expected: start times precise enough to order this test against the first launch failure 1.7 s earlier, e.g. `t=133.366s` or `t=2m13.366s`.
- Got: `t=2m13s` for the test marker, `t=2m12s` for the failure; under one minute the same column prints `t=47.858s`. `marker info` (text) also prints only `Time: 2m13s - 2m58s (45.030s)`.
- Workaround: `profiler-cli marker info m-1 m-3 m-7 --json` and read `start` (in ms).
- Question it blocked: "how long after the job's first launch failure did this test start?" The list could keep millisecond precision whatever the magnitude.
