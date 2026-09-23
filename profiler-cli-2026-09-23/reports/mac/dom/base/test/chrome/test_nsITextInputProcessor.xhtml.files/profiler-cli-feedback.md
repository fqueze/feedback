## Question: whole-machine CPU during a time range (mean/median/max of CPU Use)
- Command: `profiler-cli zoom push 60,320` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a python script averaging data.cpuPercent
- Expected: an aggregate (mean/median/max CPU percent) in the default output of `thread markers --search 'name:CPU Use'` for the zoomed range, the way it shows duration stats
- Got: only duration stats; the per-marker CPU values are only in --json
- Workaround: script over --json

