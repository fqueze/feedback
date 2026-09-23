## Question: how busy was the machine, per 100 ms, around a failure (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <resource-usage session>` (zoomed to a few seconds around the failure)
- Expected: each `CPU Use` row to show its `cpuPercent` (and ideally idle %), the only thing that marker says.
- Got: rows with name, time and duration only; the payload column is empty, so the list cannot answer "was the machine saturated".
- Workaround: `--json` and a Python one-liner printing `fields[].cpuPercent` / `idle_pct` per marker.
- Also: `profile info` / `counter list` on the resource-usage profile say "No significant activity" / "No counters in this profile", which reads as an idle machine when every `CPU Use` marker in the range says 100%.

## Session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` and the load failed; the error message itself suggests `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked.
- Cost: one failed load. The brief does not mention `PROFILER_CLI_SESSION_DIR`, and `profile-link.py` reads the same variable, so it has to be set on every command.

## Question: which IPC message is this `IPCOut`, and when did its send return (review)

- Command: `profiler-cli thread markers --search IPCOut --list --limit 0 --session <id>` (zoomed to a 30 ms stall)
- Expected: each row to show the message type and seqno, plus its phase times (sent, transfer start/end, received), relative to the profile like every other time.
- Got: rows with an empty payload column. `marker info` shows the type, but the phase times appear only under "Other payload fields (no schema)" as raw `startTime`/`sendStartTime`/`sendEndTime`/`recvEndTime`. These are offset by the profile's zero (32.5 ms here), so they have to be converted by hand before they can be compared with anything else.
- Workaround: `marker info` on each handle, then subtracting the offset between the payload `startTime` and the marker's displayed time.
- Also hit again: `EPERM` on `~/.profiler-cli` until `PROFILER_CLI_SESSION_DIR` was set (see above).
