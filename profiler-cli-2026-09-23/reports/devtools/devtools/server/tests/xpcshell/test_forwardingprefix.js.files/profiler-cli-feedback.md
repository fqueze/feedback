## The millisecond start time of a marker
- Command: `profiler-cli marker info m-1 m-3 m-6 --session …` (resource-usage profile, 3m37s long)
- Expected: start times precise enough to say "31 ms after the test started" (the gap between a `test` marker and its `will retry` INFO).
- Got: `Time: 2m13s - 2m58s (45.030s)` and `Time: 2m13s (instant)`: rounded to the second, so the two are indistinguishable.
- Workaround: `thread markers --search … --list --json` and reading `start`.

## How many test markers had each status before and after a given time
- Question: "how many tests passed / timed out after the first launch failure at t=131.676 s".
- Command: `thread markers --search name:test --list --limit 0 --json` piped to a script bucketing `data.status` by `start`.
- Could have shown: a count per `status` field of the `test` markers in a zoomed range (not tried: `zoom push` + `--group-by field:status`). The `name:test` search also returns the harness selftests' Test markers and the `selftests` Text marker, which the script had to drop by requiring a path in the `test` field.
