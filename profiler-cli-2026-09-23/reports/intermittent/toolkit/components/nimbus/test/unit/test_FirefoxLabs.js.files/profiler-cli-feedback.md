## Question: what was the whole-machine CPU use while this test ran?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 1768.5,1800`) on a resource-usage profile.
- Expected: the CPU percent of each sample, or a summary (mean/max CPU over the zoomed range).
- Got: 253 rows of `CPU Use  t=29m29s  83ms` with no value; `counter list` says "No counters in this profile" and `profile info` says "No significant activity", so neither answers it either.
- Workaround: `--json`, then a Python script weighting `data.cpuPercent` by `duration` per minute and over the test's window.
- What could have shown it: print the `cpuPercent` field in the `--list` row for `CPU` markers, or have `profile info` / `counter list` treat CPU Use markers as the machine CPU track of a resource-usage profile.

## Minor: `marker info <handles...> --json` records have no `handle`

- Command: `profiler-cli marker info m-1 m-18 m-509 --session <s> --json`
- Expected: each record to carry its handle, to match them back to the request.
- Got: `handle` missing (printed `None`); I relied on the order.

## Question: which kind of child process hung at shutdown, for each hang in the job? (review-test_FirefoxLabs.js)

- Command: `profiler-cli thread markers --session <s> --search 'may be hanging' --list --limit 0` on a resource-usage profile.
- Expected: the process type (GPU, content, RDD…) of each pid that ProcessWatcher waits for.
- Got: pids only. To get the types, I listed `--search 'Main Thread]'` output markers and joined their `[GPU <pid>, …]` prefixes to the hanging pids with a script.
- What could have shown it: `profile info` or a `thread markers` summary that maps each child pid seen in log prefixes to its process type.
