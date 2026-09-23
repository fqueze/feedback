## Was the machine saturated during a time range? (resource-usage profile)

- Command: `profiler-cli thread markers --session RSC_get-1 --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing the `cpuPercent` field per 10 s / per minute.
- Question: "what was the whole-machine CPU use between 16m and 19m30s, and per minute over the job?"
- Expected: `profile info` / `counter info` to summarise CPU over the zoomed range, as it does for counters. `counter list` says "No counters in this profile": the resource-usage profile carries CPU only as `CPU Use` interval markers.
- What its output could have shown: a per-bucket average/min of a numeric marker field over the view (e.g. `thread markers --search "name:CPU Use" --stats-field cpuPercent --buckets 60s`), or `profile info`'s "CPU activity over time" computed from those markers.

## How many tests were running at time t? (resource-usage profile)

- Command: `thread markers --search "name:test" --list --limit 0 --json`, then a script counting `test` intervals overlapping t.
- Question: "which tests were running in parallel with mine when its child hung?"
- What its output could have shown: `thread markers --search name:test --overlapping <t>` (or `--at <t>`), listing the interval markers that contain t.

## When did a marker happen across the job? (histogram)

- Command: `thread markers --search "may be hanging at shutdown" --list --limit 0 --json`, then a per-minute count in Python.
- Question: "are the child-hang warnings clustered in time, and do they line up with CPU saturation?"
- What its output could have shown: a `--group-by time:60s` (count per time bucket) on `thread markers`.

## Default session directory not writable (sandbox)

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (and for `profile-link.py`). The error message said exactly this, so it cost one call; the brief does not mention it.
