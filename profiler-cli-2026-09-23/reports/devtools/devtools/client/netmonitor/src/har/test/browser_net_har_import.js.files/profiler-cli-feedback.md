## Machine CPU while one test ran (resource-usage profile)

- Question: "was the machine busy during this test's 45 s timeout?"
- Command: `profiler-cli zoom push m-1 --session <s>` (the `test` marker), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`.
- Expected: a summary of the `CPU Use` counter-like markers over the zoomed range (mean / max per bucket, like `counter info` does for counters).
- Got: 455 individual markers; the percentage is only in each marker's fields.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per 5 s bucket.
- Could have shown: `thread markers --search "name:CPU Use" --stats cpuPercent` or `counter`-style "over time" buckets for numeric marker fields.

## Markers after a given time

- Question: "which activity runnables ran after t=2.8 s, for the rest of the profile?"
- Command: `thread markers --list --search ...` piped to awk on the `t=` column.
- Got: string comparison of `t=16.563s` vs `t=2.8s` silently drops later markers; `zoom push 2.8,<end>` is the right way but needs the end time.
- Could have shown: `--from <t>` / `--to <t>` on `thread markers`, or `zoom push 2.8,end`.
