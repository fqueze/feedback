## `thread samples` keeps the popped/cleared zoom range

- Commands: `profiler-cli zoom push 54.97,59.57 --session S`, `thread samples --session S`, then `profiler-cli zoom clear --session S` and `thread samples --include-idle --session S` again.
- Expected: after `zoom clear`, the header says "View: Full profile" and the samples cover the full profile (5397 samples on this thread).
- Got: the header said "View: Full profile", but the samples were still those of the cleared zoom range (383 samples, the same categories). Running it again gave the same stale result.
- Workaround: `zoom push 0,<end>` explicitly, then `zoom pop`.
- Why it matters: the stale output looked like a real full-profile result. I nearly used it as evidence.

## Question: what was the machine CPU (and IO wait) during this test? (review)

- Command: `profiler-cli zoom push 280.3,344.5 --session S`, then `thread markers --search "name:CPU Use" --list --limit 0 --json --session S`, piped through a Python script averaging `cpuPercent` / `iowait_pct` per 5 s bucket.
- Expected: the default `thread markers --search "CPU Use"` output (the aggregated view) to give the mean/min/max of the CPU Use payload fields in the zoomed range, or a counter-like summary as `counter info` gives for Process CPU.
- Got: 644 individual markers, one per 100 ms, with the values only in `marker info` or `--json`.
- Workaround: script over `--json`. On a resource-usage profile, this is the question asked for every timeout.
