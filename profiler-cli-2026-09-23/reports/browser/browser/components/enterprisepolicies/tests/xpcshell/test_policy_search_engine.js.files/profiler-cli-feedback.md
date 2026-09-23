## Question: how busy was the whole machine during a given window of a resource-usage profile?

- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list` (after `zoom push 1150,1215`)
- Expected: the CPU percentage per sample, or a summary of it over the zoomed range.
- Got: one row per `CPU Use` marker with only its time and duration; the `cpuPercent` field is only visible via `marker info` one marker at a time, and the profile has no counters (`counter list`: "No counters in this profile"), so `profile info` reports "No significant activity" for a job whose CPU was at 95-100%.
- Workaround: `--list --limit 0 --json` piped to a Python script bucketing `data.cpuPercent` by 5 s. Could have shown: the payload's CPU% in the list row, or treating resource-usage `CPU Use` markers as a counter so `counter info` gives the over-time table.
