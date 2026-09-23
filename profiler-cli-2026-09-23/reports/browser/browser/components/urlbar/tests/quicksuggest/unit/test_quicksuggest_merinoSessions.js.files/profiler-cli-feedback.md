## Question: what was the machine's CPU use over a time range (resource-usage profile)?
- Command: `profiler-cli thread markers --search "name:CPU Use" --list` (after zoom push).
- Expected: a CPU-over-time summary, e.g. mean CPU % per second or per bucket, like `counter info` gives for counters.
- Got: one row per sample with no values. The CPU % is only visible through `marker info` on each one. There are about 5 markers per 0.5 s, with overlapping durations.
- Workaround: `--json` then jq/awk to take a duration-weighted mean per second. `profile info` says "No significant activity" for this profile, because the CPU lives in markers rather than samples or counters.
