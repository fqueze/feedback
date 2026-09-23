## Question: "at which millisecond did each marker happen?" (ordering markers inside a fast test)

- Command: `profiler-cli thread markers --search "RDP Front,DevTools:" --list --limit 0 --session <id>`
- Expected: timestamps precise enough to order markers. The whole test took 680 ms, and the race was 2 ms wide.
- Got: `t=1m41s` / `t=1m42s` on every row. The durations (`3.456ms`) are precise but the start times are not, so the list cannot answer "did the getMutations reply come before or after this getApplied request".
- Workaround: `--json` piped to a python script that prints `start` and `duration`. I needed this for about ten queries in this investigation. When the view or zoom spans less than a few seconds, printing start times in ms (e.g. `101543.60`) would have answered it.
