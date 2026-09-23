## Question: in what order, to the millisecond, did these log lines happen?

- Command: `profiler-cli thread markers --session <s> --search <text> --list --limit 0` on a 56-minute resource-usage profile.
- Expected: timestamps precise enough to order events that are 10–300 ms apart (here: `ToolUI: Checking if retry injection needed` 138 ms before `Leaving test …`, then an FxA warning 150 ms later).
- Got: `t=43m35s` for all of them — whole seconds once the profile is longer than a minute, so the order and gaps that decide the race are invisible.
- Workaround: `--list --json` piped through a script printing `start/1000` with 3 decimals, or `marker info m-… --json`.
- What the output could have shown: millisecond timestamps (e.g. `t=43m35.311s`), at least when `--search` narrows the list, or a `--precise-times` flag.

## `marker info` JSON shape differs between one handle and several

- Command: `profiler-cli marker info m-11 --json` vs `profiler-cli marker info m-11 m-311 --json`.
- Expected: the same shape, so one script handles both.
- Got: a bare marker object for one handle, `{"type": "marker-info-multi", "markers": [...]}` for several; my script broke on the switch.
- Workaround: branch on `type`.
