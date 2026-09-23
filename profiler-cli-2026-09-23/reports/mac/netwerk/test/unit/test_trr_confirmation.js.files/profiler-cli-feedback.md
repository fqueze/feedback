## Default session directory not writable in the sandbox
- Command: `profiler-cli load <url> --session test_trr_confirmation.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` (from STATUS.md).

## Was the machine saturated at time T? (resource-usage profile)
- Commands: `profile info` said "No significant activity", `counter list` said "No counters"; `thread markers --search 'name:CPU Use' --list` printed rows with `t=1m51s` (1 s resolution in a 37 min profile) and no CPU percentage.
- Workaround: `--list --json` and a script printing `start` and `data.cpuPercent`/`idle_pct` for the markers in a zoom, and a mean over the test's run (100% CPU, 0% idle in all 122 samples).
- What could have shown it: the CPU percent in the list row, sub-second times when zoomed, or a CPU summary in `profile info` from the CPU Use markers.

## When was this sample taken?
- Question: at what time is the one sample inside a 4.5 ms window (to know whether `setIntPref` was still running at a given point).
- Commands: `thread samples --include-idle` in the zoom shows the stack but no time; bisected with repeated `zoom push`/`zoom pop` and `thread samples --json`.
- What could have shown it: sample timestamps in `thread samples` output when the view holds few samples, or a `thread samples --list`.

## Sub-millisecond gaps between markers
- Question: the gap between `Preference Write network.trr.mode: 2` and the following `TEST-UNEXPECTED-FAIL` (4.49 ms, against 0.07-0.78 ms for the passing steps).
- `thread markers --list` rounds to `t=6.986s` / `t=6.990s`; needed `marker info <m> --json` per marker for `start`.
- What could have shown it: more digits in `--list` when zoomed to a few ms, or a `+Δ` column from the previous row.
