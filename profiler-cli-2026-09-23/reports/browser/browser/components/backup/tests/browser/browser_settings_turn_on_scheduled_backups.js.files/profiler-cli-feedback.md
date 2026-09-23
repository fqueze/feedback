## Question: how long did each step of the test take (gaps between consecutive log lines)?

- Command: `profiler-cli thread markers --session <s> --search turn_on_scheduled --list --limit 0` on a resource-usage profile.
- Expected: timestamps precise enough to see which step took 10 s.
- Got: past one minute, times print as `t=5m46s` (1 s resolution), so a dozen lines share one timestamp and gaps under a few seconds vanish.
- Workaround: `--json` and a Python script computing start deltas between consecutive markers. A `--list` time column with ms (or a `+delta` column) would have answered it directly.

## Question: what time range does this profile actually hold data for?

- Command: `profiler-cli profile info` / `thread markers --list` on a per-test profile (`VYJvaz8FQ3qEqLumSuEASA`, `profile_browser_settings_turn_on_scheduled_backups.js.json`).
- Expected: a warning that the parent process buffer wrapped. It holds 7M `DocAccessible::ContentRemovedNode` markers and only the last ~5 s of the 1m39s session.
- Got: `Full: 1m39s`, with nothing saying samples and markers on t-0 only start around 1m34s. I only found out when a zoom on the 10 s window before that returned "No samples in the current view" and the test log started in the next-to-last task.
- Suggest: `profile info` could print, for each thread, the first sample time and first marker time when they come well after the profile start.

## Question: precise start/end of a few markers

- Command: `profiler-cli marker info m-18 m-20 m-25 m-30`
- Got: `Time: 1m24s - 1m37s (13.720s)`. The duration is precise but start and end are rounded to the second, so I could not line them up with the resource-usage profile's test log.
- Workaround: `marker info ... --json`.
