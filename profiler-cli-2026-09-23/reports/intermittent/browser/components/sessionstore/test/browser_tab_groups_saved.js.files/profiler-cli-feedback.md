
## profiler-cli: marker times at 1 s resolution in --list on long profiles
- Command: `profiler-cli thread markers --category Test --search browser_tab_groups_saved --list --limit 0 --session ...` on a 9m56s profile.
- Expected: timestamps precise enough to order markers a few ms apart (e.g. `t=592.204s`).
- Got: `t=9m52s` for every marker from 592.20 to 592.42 s; `marker info` also prints `Time: 9m52s`.
- Workaround: `--json` and read `start`.

