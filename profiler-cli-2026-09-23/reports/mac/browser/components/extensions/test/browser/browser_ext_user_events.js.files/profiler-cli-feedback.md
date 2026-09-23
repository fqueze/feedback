## Sub-second times hidden in `thread markers --list` after the first minute

- Command: `profiler-cli thread markers --session browser_ext_user_events.js-1 --category Test --search browser_ext_user_events --list --limit 0`
- Expected: start times precise enough to order markers a few ms apart (e.g. `t=319.671s`).
- Got: `t=5m18s`, `t=5m20s`, `t=5m29s` for every marker past the first minute, so the test's own log, the vsync wait and the refresh ticks could not be ordered.
- Workaround: `--json` and a script printing `start/1000` with 3-4 decimals.
- Question behind the script: "in what order, to the millisecond, did these markers happen?" The list could print seconds with ms (`t=319.671s`) or `5m19.671s`.

## First/last time per group in `--group-by`

- Command: `profiler-cli profile markers --session browser_ext_user_events.js-1 --search RefreshDriverTick` (zoomed to the vsync wait), then `thread markers --group-by name,field:innerWindowID`.
- Question: "during this wait, when did each thread's / each document's refresh ticks stop?" (the parent's stopped at 319.866 s, the leaked popup's ran to the end).
- Got: counts and example handles per group, no first/last start time.
- Workaround: `--json --limit 0` and a script taking min/max of `start` per (thread, name, innerWindowID).
- Could show: `first=… last=…` per group line.
