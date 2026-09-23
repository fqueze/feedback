## Question: "how busy was the machine during this one test's 45 s?" (resource-usage profile)

- Command: `profiler-cli zoom push 52.259,97.267 --session S; profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging the `cpuPercent` field of 450 markers.
- Expected: the default (non-JSON) `thread markers --search "name:CPU Use"` in a zoom, or `profile info` in a zoom, to give the mean/max of the `CPU Percent` field over the range. `profile info` says "No significant activity" for these profiles, because the machine CPU is in markers, not samples.
- Got: only per-marker rows (450 of them), no summary of the numeric field.
- Workaround: `--json` plus a script. A `--stats <field>` (mean/min/max of a numeric payload field over the filtered markers) would answer it.
