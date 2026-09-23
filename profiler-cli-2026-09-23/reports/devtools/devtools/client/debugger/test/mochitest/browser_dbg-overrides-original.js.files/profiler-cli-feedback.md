## Question: in what order did these markers happen within one second?

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_dbg-overrides-original.js --list --limit 0` (also with `--search DOMEvent`, and inside `zoom push 256.16,256.21`).
- Expected: start times precise enough to order markers within a second, e.g. `t=256.1972s`.
- Got: past one minute, every start time is rounded to whole seconds (`t=4m16s`). Even inside a 50 ms zoom, the selectSource step, the right-click's layout flushes, the `contextmenu` DOMEvent and `popupshown` all read `4m16s`.
- Workaround: `--json` piped into a script that prints `start/1000` with 4 decimals. I needed it for every list in this investigation.
- What the output could have shown: millisecond start times (`256.197s`), or precision that follows the zoom range.

## Question: was the machine busy during this window? (resource-usage profile)

- Command: `profiler-cli profile info` and `profiler-cli counter list` on `profile_resource-usage.json`.
- Expected: the machine's CPU over time.
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.`, although the profile holds 23,617 `CPU Use` markers with `cpuPercent`.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped into a script that averages `cpuPercent` over time windows.
- What the output could have shown: `profile info` summarising `CPU Use` markers the way it summarises sampled CPU, or a `--range` average.
