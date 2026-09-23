## `thread markers --search` with only exclusion terms matches nothing

- Command: `profiler-cli thread markers --session <s> --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-CONSOLE_MESSAGE,-head.js" --list --limit 0` (with a zoom pushed)
- Expected: every marker in the zoom except the excluded ones.
- Got: "0 markers (filtered from 130)… No markers match the specified filters."
- Workaround: add positive terms (`name:test,name:INFO,name:ERROR,…`) before the exclusions.

## Question: was the machine busy or idle while the tests "timed out"? (resource-usage profile)

- Command: `profiler-cli profile info` / `thread markers --search "name:CPU Use" --list` on a resource-usage profile
- Expected: machine CPU over time.
- Got: `profile info` says "No significant activity", `counter list` says "No counters"; the CPU Use list rows show no value (it is only in `marker info` per marker). Needed a script over `--json` to bucket cpuPercent by 5 s.
- Could have shown: the CPU Use value in the list row label, or a CPU-over-time summary for resource-usage profiles in `profile info`.

## (review) Question: which tests started but never ended? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:test,will retry" --list --limit 0 --json`, then a script (review-split.py / review-timeline.py here) to pair each "will retry" INFO line with a `test` marker.
- Expected: some way to see tests that logged `test_start` but no `test_end`.
- Got: such tests have no `test` marker at all, so they are invisible in the list; only their "will retry" INFO line shows. In the three jobs checked, 283–310 of them came before the TIMEOUT ones, and the report missed them.
- Could have shown: a marker (or a `profile info` line) for tests with a `test_start` and no `test_end`, from the start time to the end of the profile.
