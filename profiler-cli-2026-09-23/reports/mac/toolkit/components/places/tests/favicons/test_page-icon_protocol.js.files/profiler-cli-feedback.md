## Question: "was the machine saturated while this test ran, and was it in the retry?"

- Command: `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --session <s>` on a resource-usage profile (after `zoom push <test marker>`).
- Expected: the CPU percentage of each `CPU Use` marker, or a summary over the zoomed range (avg/min/max CPU %, share of time at 100%).
- Got: one row per marker with only time and duration; the `cpuPercent` / `idle_pct` fields are not in the list. `counter list` says "No counters in this profile", so there is no counter to `counter info` either.
- Workaround: `--json`, then a Python script bucketing `data.cpuPercent` per 2 s / per minute.
- What would have answered it: a per-bucket "over time" summary for `CPU Use` markers like `counter info` prints for counters, or the payload's key field in the `--list` description column.

## Environment: default session directory is not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (and on `profile-link.py`). The subagent brief does not mention it; one lost round trip.
