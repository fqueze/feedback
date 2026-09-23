## Precise time between two markers in a long profile

- Question: how many seconds after "Application command" did "Entering test" happen (a ~2 s gap, in a 28 min profile)?
- Command: `profiler-cli thread markers --session <s> --search "Application command,Entering test" --list`, then `profiler-cli marker info m-9 m-10`
- Expected: millisecond start times, or a delta.
- Got: `t=5m59s` / `t=6m1s` and `Time: 20m16s (instant)`: rounded to whole seconds once the profile is over a minute long.
- Workaround: `marker info --json` piped to python to read `start`. A ms-precision time in `marker info` text (or a `--relative-to m-N` option on `--list`) would answer it.
