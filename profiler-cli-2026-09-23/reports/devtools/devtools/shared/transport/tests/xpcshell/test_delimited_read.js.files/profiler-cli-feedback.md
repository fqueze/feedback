## `--search` does not match the text a marker list displays

- Command: `profiler-cli thread markers --session test_delimited_read.js-1 --search "PASS —" --list --limit 0` on the resource-usage profile of XfKT04wZR42kSNwV6wuRpQ.
- Expected: the `test` markers the list shows as `PASS — xpcshell-remote.toml:…`.
- Got: `0 markers`. The displayed text is a formatted label, and `--search` does not match it.
- Workaround: `--search status:PASS`, found after `--group-by field:status`.

## Question: what was the machine's CPU use over time, in a resource-usage profile?

- Command: `profiler-cli profile info --session test_delimited_read.js-1`.
- Got: `CPU activity over time: No significant activity.` The one thread has no samples. But the profile's 1163 `CPU Use` markers show the machine at 100% from t=40 s to t=55 s and at 1–2% after t=60 s.
- Workaround: `thread markers --search "CPU Use" --list --limit 0 --json`, then a script to average `data.cpuPercent` over 5 s buckets.
- What would have answered it: `profile info` (or `counter list`) summarizing the `CPU Use` markers over time in a resource-usage profile, the way it does sampled CPU.
