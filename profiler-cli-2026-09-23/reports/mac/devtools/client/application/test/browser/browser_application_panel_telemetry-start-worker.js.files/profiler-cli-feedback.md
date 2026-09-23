## Question: the exact order of a test's log lines, a few ms apart, across processes

- Command: `profiler-cli thread markers --search telemetry-start-worker --list --limit 0 --session <s>` on a resource-usage profile.
- Expected: timestamps precise enough to order the lines (they are 1-10 ms apart), and the originating process/pid/thread of each C++ warning.
- Got: `t=1m29s` on every row across 1.5 s of events, and no pid/thread column (only visible per marker via `marker info`).
- Workaround: `--list --limit 0 --json` then a script printing `start/1000` and `data.pid`/`data.thread`.
- Could have shown: ms precision when the listed range is short (or a `--precise`/`--relative-to <marker>` option), and a process/thread column when the payload has one.

## Question: which marker handle is at marker index N (checking a report's `marker=N` links)

- Command: `profiler-cli marker info m-N --json --session <s>`, once per link (about 30 links over four profiles), then a script to pull out `markerIndex`.
- Expected: a way to go from a link's `marker=N` to the marker, or the index printed with each row.
- Got: `markerIndex` only in `marker info --json`. The text output of `marker info` and `thread markers --list` leaves it out.
- Workaround: a shell loop over the handles, piping each `--json` result into python.
- Could have shown: the index in `marker info` text output and as a `--list` column, or `marker info --index N`.
