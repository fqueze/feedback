## Question: "was the machine CPU-saturated while this test ran?" (resource-usage profile)
- Command: `profiler-cli profile info --session <s>` on a `profile_resource-usage.json`; then `profiler-cli counter list`.
- Expected: some whole-machine CPU summary for the view, since that is the main reason to open a resource-usage profile.
- Got: `profile info` says "No significant activity" and `counter list` says "No counters in this profile". The CPU data is actually in ~20,000 `CPU Use` markers (cpuPercent field), which only show one by one via `marker info`.
- Workaround: `thread markers --search 'name:CPU Use' --list --limit 0 --json` plus a Python script averaging `data.cpuPercent` per second/minute.
- What could answer it: `profile info` (or `counter list`) summarising `CPU Use` markers as a time series within the current zoom — e.g. per-bucket average CPU %, like `counter info` "over time".

## Question: "which tests in this job hit the same failure, and when?" (resource-usage profile)
- Command: `profiler-cli thread markers --search 'attempting crash report' --list --limit 0`.
- Expected: the `test` payload field shown in the list (which test each marker belongs to).
- Got: list rows show the message only; the Test field appears only via `marker info` or `--json`.
- Workaround: `--json` and a script grouping by `fields[key=test]` and by minute.
- What could answer it: `--group-by field:test` works for aggregation, but a list column for the Test field (or `--list --group-by field:test`) would have answered directly.
