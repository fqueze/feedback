## `thread markers --list` time column too coarse to order events a few ms apart

- Command: `profiler-cli thread markers --session test_register_actor.js-1 --search test_register_actor --list --limit 0` (xpcshell resource-usage profile, 3m37s long)
- Question: when did the test start, and how long after its start did the harness log "will retry"?
- Expected: start times precise to the millisecond (or at least 0.1 s), since the answer here was 39 ms.
- Got: `t=2m13s` for both the `test` marker and the `INFO ... will retry` marker; the order and the gap are invisible.
- Workaround: `--json` and read `flatMarkers[].start`.

## (review) Status counts of the `test` markers that start after a time

- Question: of the tests that started after t=131.6 s, how many ended PASS / TIMEOUT / SKIP?
- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json --session review-test_register_actor.js-1`, then a Python script over `flatMarkers[].start` and `data.status`.
- What could have answered it: a way to filter by start time (`zoom push` also keeps markers that only overlap the range), plus a per-field value count in the aggregated view, such as `status: TIMEOUT 482, SKIP 28`.
