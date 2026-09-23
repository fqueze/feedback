## Question: how many ms after a test's start did the harness log "will retry"? (resource-usage profile, late in the job)

- Command: `profiler-cli thread markers --session test_addon_events.js-2 --search test_addon_events.js --list --limit 0`, then `profiler-cli marker info m-1 m-3`
- Expected: start times precise enough to subtract (ms), as they are before the 1-minute mark (`t=46.324s`).
- Got: past one minute both `--list` and `marker info` round to whole seconds: `t=2m13s` for the test marker and `t=2m13s` for the INFO 37 ms later, and `Time: 2m13s - 2m58s (45.040s)`. The interval I needed (37 ms) is invisible.
- Workaround: `marker info m-1 m-3 --json` and read `start` (132976.908 / 133013.908).
- Could have shown: `2m12.977s`, i.e. keep ms precision in `m`/`s` formatted times, at least in `marker info`.
