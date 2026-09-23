## Question: "at which millisecond did this marker start?" (for markers past 1 minute)

- Command: `profiler-cli thread markers --search test_framebindings-07.js --list --limit 0 --session <id>` and `profiler-cli marker info m-1 m-3 m-4 m-6 --session <id>`
- Expected: start times precise enough to compare with neighbouring markers (e.g. `t=92.691s`), as they are below 1 minute (`t=47.653s`).
- Got: `t=1m33s` in the list and `Time: 1m33s (instant)` in `marker info`; a 30 ms gap (test start vs `will retry`) or the order of Begin/ERROR/End cannot be read past t=60s.
- Workaround: `--list --json` piped to python to print `start`.
