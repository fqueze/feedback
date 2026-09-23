# fx-tests feedback (test_extensionsettings.js)

## Question: "what timeout did this test run under, on each config?"

- Command: `fx-tests test <path> --durations --limit 0`
- Expected: next to the per-config durations, the harness timeout of that config (xpcshell: 30 s times the
  job's `--timeout-factor`, times the manifest's `requesttimeoutfactor`).
- Got: durations only. For a TIMEOUT-only test the answer hinges on the margin between the two, and it had
  to come from downloading a 15 MB `live_backing.log` per job and grepping `Using harness timeout of 30s
  (base=30s, factor=1.0)`.

## Question: "how long does this test take in the parallel phase on config X?"

- Command: `fx-tests test <path> --durations --limit 0`
- Got: `test-linux2404-64-artifact/debug-xpcshell  66 runs  min 6594  median 6942  p95 31045  max 31710`.
  The passing runs mix the sequential harness retries (about 7 s) with the few parallel passes (about 31 s),
  so the median describes the retries, not the runs that fail. A per-mode split (as `--executions` does for
  failures) would have shown the parallel runs sit at the 30 s limit.
