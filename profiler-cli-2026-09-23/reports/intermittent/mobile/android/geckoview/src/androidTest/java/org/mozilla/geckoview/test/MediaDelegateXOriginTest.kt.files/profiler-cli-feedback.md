# profiler-cli feedback (MediaDelegateXOriginTest.kt, bug 1990004)

## Question: "how busy was the machine during each test, before and after the hang?" (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on FLIPpavAQHmAHmFRrDz-qQ's `profile_resource-usage.json`.
- Expected: each CPU Use marker's `cpuPercent` in the row, or per-range min/median; `counter list` or `profile info` treating it as CPU activity.
- Got: rows with no value; `profile info`/`thread info` say "No significant activity"; `counter list` says "No counters".
- Workaround: `--json` dump (44 MB) plus a Python script joining CPU Use markers with `test` markers.
- Could have shown: `cpuPercent` in the list row, and a "CPU during marker" summary (e.g. `thread markers --search name:test --with-cpu`).
