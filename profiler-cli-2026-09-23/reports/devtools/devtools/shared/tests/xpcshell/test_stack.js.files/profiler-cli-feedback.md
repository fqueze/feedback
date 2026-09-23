# profiler-cli feedback (test_stack.js)

## `--list` rounds times past one minute to the second

- Command: `profiler-cli thread markers --session test_stack.js-1 --search "INFO,DEBUG" --list --limit 0` (a 21-minute xpcshell resource-usage profile)
- Expected: marker times precise enough to order and subtract markers that are ms apart.
- Got: `t=3m31s` for every marker in that second, so the `ps` snapshot 2 ms before the failure message could not be told apart from the ones around it.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals.

## Question needing a script: which harness slot processes existed at each time

- Question: "in each `get_process_list` DEBUG snapshot, which `:xpcshellN` processes are listed?", to see whether a test's slot process existed when the harness said it did.
- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --json` + a regex over each label.
- What could have answered it: a `--search` that shows where in a long label the match is (a snippet around the hit), instead of the label's first 250 characters, which are always `init`, `kthreadd`, ...

## Question needing a script: test markers with start and end

- Question: "which tests held a slot at t=209.17 s?", which needs each `test` marker's start and end.
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then `start` + `duration` per marker.
- What could have answered it: an end time (or `start → end`) column in `--list` for interval markers.
