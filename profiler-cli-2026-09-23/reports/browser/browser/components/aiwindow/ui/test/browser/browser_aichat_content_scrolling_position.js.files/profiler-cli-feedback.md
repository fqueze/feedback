## Question: what was the machine's CPU use during one test (resource-usage profile)?

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --session S` (zoomed on the test's `test` marker).
- Expected: one row per sample with its CPU %, user % and system %.
- Got: rows with only name, time and duration; the percentages are only in `marker info`. `counter list` says "No counters in this profile" and `profile info` says "No significant activity", so neither shows the CPU either.
- Workaround: collect the handles from `--list --json`, pass them all to `marker info --json`, and extract `fields[].cpuPercent/user_pct/system_pct` with jq.
- Could have shown: the marker's main payload fields (CPU %, user/system split) in the `--list` row, or a per-bucket CPU summary for the resource-usage profile in `profile info` / `counter info`.


## Question: which IPC message is each `IPCIn` row? (review)

- Command: `profiler-cli thread markers --search IPCIn --list --limit 0 --session S` on a child main thread.
- Expected: each row naming its message type (`PContent::Msg_ConstructBrowser`, `PBrowser::Msg_LoadURL`...), as the profiler's marker table does.
- Got: 187 rows labelled `IPCIn` with an empty description; `--search ConstructBrowser` finds them but still prints no type.
- Workaround: collect all handles from `--list --json`, pass them to `marker info --json`, extract `fields[messageType]` with jq.
- Could have shown: the message type (and other pid) in the row description.

