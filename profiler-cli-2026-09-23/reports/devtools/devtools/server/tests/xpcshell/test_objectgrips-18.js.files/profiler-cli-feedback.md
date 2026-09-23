## `thread markers --list` rounds times to the second past one minute

- Command: `profiler-cli thread markers --session test_objectgrips-18.js-1 --search test_objectgrips-18 --list --limit 0`
- Expected: millisecond times, to tell that the test's `will retry` came 37 ms after its `test` marker started, and to order it against the first `Failed to launch` warning.
- Got: `t=2m13s` for both, and `t=2m12s` for the launch failure.
- Workaround: `marker info <m> --json | jq .start`, one call per marker, or `--list --json` and `start/1000`.

## Question: which of this job's tests started in each second, with what status?

- Question: "how many tests started in the second after the first failed launch, and how did they end?"
- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json | jq '... select(.markerType=="Test") | [(.start/1000|floor), status]' | sort | uniq -c`
- What could have shown it: a `--group-by field:status` combined with time buckets (`--bucket 1s`) on `thread markers`. Also, `name:test` matches the harness's phase `Text` markers named `test`, so a `type:Test` filter was needed.
