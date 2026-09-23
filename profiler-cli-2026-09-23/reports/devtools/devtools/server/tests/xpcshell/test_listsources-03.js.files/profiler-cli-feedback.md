## Question: "what was the machine's mean CPU while this test ran?"

- Command: `profiler-cli zoom push 133.228,178.269 --session … ; profiler-cli thread markers --search "CPU Use" --list --limit 0 --json` on a resource-usage profile (LKys8j8iTQKTAN9hKv040g).
- Expected: a summary of the `CPU Use` markers in the zoomed range (duration-weighted mean / min / max of `cpuPercent`), or a counter track for machine CPU that `counter info` would summarize.
- Got: 452 individual markers; `counter list` says "No counters in this profile", and the default `thread markers --search "CPU Use"` aggregate gives durations, not the payload value.
- Workaround: a Python script over `--json` computing the duration-weighted mean of `data.cpuPercent` clipped to the range (`cpu-mean.py` here). Every xpcshell timeout report needs this number.

## Question: "how many tests in this job are fake 45 s timeouts, and when did they start?"

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json`, then a script filtering `duration` in 45000–45100 ms.
- Expected: `thread markers --search name:test --group-by field:Status` with a duration histogram, or `--min-duration/--max-duration` on `--list` giving a count and the first/last start.
- Got: no max-duration filter on markers; had to script it.
- Workaround: Python over `--json`.

Correction to the second item: `thread markers --min-duration 45000 --max-duration 45100 --search name:test` exists and gives the count (482) without a script; `--max-duration` for markers is in `thread markers --help` but not in the `guide` (which lists it only under `thread network`). What the aggregate still lacks is the first and last start time of the matching markers.
