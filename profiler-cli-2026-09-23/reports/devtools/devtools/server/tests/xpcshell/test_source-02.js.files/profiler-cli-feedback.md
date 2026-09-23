## Question: "how many ms after marker A did marker B happen?"

- Command: `profiler-cli thread markers --session test_source-02.js-1 --search test_source-02.js --list --limit 0` on a 3m37s resource-usage profile.
- Expected: start times precise enough to compare nearby markers (the test started at 133.458 s, its "will retry" came 37 ms later, the first launch failure 1.78 s before).
- Got: `t=2m13s` for both markers; `marker info` also prints `Time: 2m13s (instant)`. Sub-second ordering is invisible in the default output.
- Workaround: `--json` and read `flatMarkers[].start`. The list could print milliseconds (e.g. `t=133.458s`) whenever two listed markers share the same rounded value, or always under `--list`.

## Question: "which tests started in this time window, and with which status?"

- Command: `profiler-cli thread markers --session test_source-02.js-2 --search "name:test" --list --limit 0` (1,000+ `test` markers).
- Expected: a way to zoom to a window and count test markers by status (TIMEOUT / PASS / SKIP) there.
- Got: a flat list too long to read; needed `--json` plus a script to count statuses between two timestamps and to find the test's rank among the TIMEOUTs.
- Workaround: script over `--json`. A `--group-by field:status` that respects `zoom push` on `test` markers would have answered it.
