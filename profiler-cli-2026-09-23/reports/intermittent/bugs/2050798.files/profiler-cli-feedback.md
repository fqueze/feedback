## Question: what was the machine's CPU use over this range? (resource-usage profile)

- Commands: `profiler-cli profile info --session 2050798-1`, then `profiler-cli zoom push 624.0,627.5` and `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: CPU use over time, since that is what a resource-usage profile is for.
- Got: `profile info` says "CPU activity over time: No significant activity" and `counter list` says "No counters", while every `CPU Use` marker in the range carries `CPU Percent: 100.0%`. The `--list` rows show no payload, so the percentages needed one `marker info` per marker.
- Workaround: `... --list | rg -o "m-[0-9]+" | xargs profiler-cli marker info ... | rg "CPU Percent"`.
- What the output could show: the `CPU Percent` field in the `--list` row of `CPU Use` markers, or a CPU summary for resource-usage profiles in `profile info`.
