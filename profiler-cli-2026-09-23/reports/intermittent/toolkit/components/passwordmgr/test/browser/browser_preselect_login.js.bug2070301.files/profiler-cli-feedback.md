## Default thread after `load` is not the parent main thread
- Command: `profiler-cli load <per-test mochitest profile URL> --session <id>`
- Got: selected thread `t-292 (GeckoMain, Privileged Content)` in one profile, `t-22 (GeckoMain, Privileged Content)` in another.
- Expected: `t-0 (GeckoMain, Parent Process)`, where the test harness markers are.
- Workaround: `thread select t-0`.

## `thread markers --list` time column is 1 s resolution past 1 minute
- Command: `profiler-cli thread markers --category Test --search browser_preselect_login --list --limit 0`
- Got: `t=2m36s` on every marker in the last second; ordering and gaps within the second unreadable.
- Expected: millisecond resolution (e.g. `t=156.011s`), as shown for times under a minute.
- Workaround: `marker info m-N --json` and read `start`.
