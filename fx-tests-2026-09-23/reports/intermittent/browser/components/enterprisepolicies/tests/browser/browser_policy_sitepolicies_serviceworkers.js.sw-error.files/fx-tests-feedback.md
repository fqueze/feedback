## `--since` takes days, not a date

- Command: `fx-tests test <path> --task-ids --issue 1 --limit 0 --since 2026-09-21`
- Expected: task IDs since that date (`--history` and `--task-ids` print dates, so a date is what one has at hand).
- Got: `--since expects a non-negative integer, got "2026-09-21"`.
- Workaround: `--since 2`.

## Question: "does this test fail on every first run, with the retry passing?"

- Commands: `fx-tests test <path>`, `--executions`, `--coverage --limit 0`
- The ~50% rate on every config is really 100% of first runs in manifest order, masked by the retry. Seeing that took arithmetic on per-config runs and fails: runs = 2 × jobs, fails = jobs + failed retries. `--executions` says "The aggregates record no order", yet a per-config "jobs / jobs with a failure / jobs whose retry also failed" split would have answered it directly.
