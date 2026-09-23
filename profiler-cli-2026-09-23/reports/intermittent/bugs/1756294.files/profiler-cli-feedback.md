## Question: "what were CPU % and memory used on the machine between t=707 s and t=710 s of a resource-usage profile, and how does that compare with the rest of the job?"

- Command: `profiler-cli zoom push 707,710 --session 1756294-1; profiler-cli thread markers --session 1756294-1 --search "CPU Use" --list --limit 0` (and the same with `name:Memory`)
- Expected: one row per sample with its value (cpuPercent / used bytes), or a summary (min/median/max) of the payload values in the zoomed range.
- Got: rows with only name, time and duration (`m-127  CPU Use  t=11m49s  110ms`); the values are only in `--json` `data`. `profile info` says "No counters in this profile" and "No significant activity", so the machine-load question has no text-mode answer at all for resource-usage profiles.
- Workaround: `--json` piped into a Python script to print `data.cpuPercent` / `data.used` per marker and compute job-wide median/p90/max.
- What the output could have shown: the payload's main field inline in `--list` rows for CPU/Memory markers, or `profile info` treating the resource-usage CPU/Memory markers as the job's CPU/memory tracks (with the job-wide distribution).
