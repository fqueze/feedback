## Question: when did the tests in this job start failing, and which tests ran last before that?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a Python script that sorted the `test` markers by start time and counted their statuses per second of start time.
- What the output could have shown: `thread markers --group-by field:status` bucketed over time (for example a `--bucket 1s` option), or a per-status count timeline for `test` markers. The default `--list` of 2843 overlapping intervals could not answer "1150 tests started between 52 s and 56 s and all timed out 45 s later".
