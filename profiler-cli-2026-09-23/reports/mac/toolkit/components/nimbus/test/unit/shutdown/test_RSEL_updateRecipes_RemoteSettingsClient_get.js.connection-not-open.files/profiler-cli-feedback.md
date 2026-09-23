## Default session directory not writable (sandbox)

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session cno-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with the suggestion to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Cost one call; the brief does not mention it.

## Precise marker order in a few milliseconds

- Command: `profiler-cli thread markers --session cno-1 --search "-name:TaskController::AddTask,-name:EventQueueInternal::PutEvent" --list --limit 0 --json` inside a 5 ms zoom, then a script printing `start` with 3 decimals.
- Question: "did the timer runnable run before or after the connection closed?" Both happened at `t=1.749s` in the text list, which rounds to 1 ms.
- What its output could have shown: more precision in `--list` times when the view is zoomed to a few ms (e.g. `t=1749.097ms`), or a `--precise` flag.

## CPU Use values over a range (review)

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0` in a 1 s zoom on a resource-usage profile, then `marker info m-149..m-159 --json` and a script to get min, max and mean of `cpuPercent`.
- Question: "how busy was the machine while this test ran?" The `--list` rows show only name, time and duration; the `cpuPercent` value of each marker is not in the text output.
- What its output could have shown: the payload's main value on each row (`100.0%`), or a summary line (min/mean/max) when every listed marker is `CPU Use`.
- Also hit again: `--list` times round to 1 ms (`t=1.749s`) inside a 17 ms zoom, so ordering the timer against `pds-datastore-changed` needed `marker info --json` (already logged above).
