## Question: what was the machine's CPU use during one test, compared with the rest of the job?

- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use'` on a mochitest `profile_resource-usage.json` (Android job XSrA3NN9SnubHz5aWquk7Q).
- Expected: `counter list` / `profile info` to expose the CPU use as a counter (the Firefox Profiler UI draws it as a track), or the marker aggregate to summarise the `cpuPercent` field (min/median/max) over the current zoom.
- Got: `counter list` says "No counters in this profile"; the marker aggregate only gives counts and interval durations, not the payload values. `profile info` says "CPU activity over time: No significant activity."
- Workaround: dumped `--list --limit 0 --json` for both `CPU Use` and `test` markers and wrote a Python script to average `cpuPercent` over each test's span.
- What could have shown it: a numeric-field summary for markers (`--stats cpuPercent`), or exposing CPU Use markers as a counter so `counter info` under a `zoom push m-<test>` answers it.
