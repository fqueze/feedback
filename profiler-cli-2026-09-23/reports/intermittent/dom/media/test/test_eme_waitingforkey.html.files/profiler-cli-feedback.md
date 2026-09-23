## Question: was the machine busy during a time window (a 160 s stall) in a resource-usage profile?

- Command: `profiler-cli zoom push 458,638 --session S; profiler-cli thread markers --session S --search "name:CPU Use"`
- Expected: some summary of the `CPU Use` markers' payload (cpuPercent mean/min/max, or a sparkline over the zoom), which is the whole point of those markers.
- Got: only duration and rate stats of the markers (all ~100 ms apart), no payload values. `counter list` says "No counters in this profile" and `profile info` says "No significant activity" (no samples), so nothing else answers it either.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` (1.5k-15k markers) and a Python script averaging `data.cpuPercent` per window.
- What the output could show: for numeric payload fields of the matched markers, mean/min/max (or per-bucket means, like `counter info`'s "over time" section).

## Question: the test's own log lines, readable

- Command: `profiler-cli thread markers --session S --search test_eme_waitingforkey.html --list --limit 0`
- Got: every row ends with ` — dom/media/test/test_eme_waitingforkey.html` (the Test Name field), and rows are cut at terminal width, so long INFO messages lose their end. When the search term is the test name, the suffix is redundant on every row.
- Workaround: redirect to a file and `sed` the suffix away.
