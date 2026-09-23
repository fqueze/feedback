## Question: at what time, to the millisecond, did each assertion of the test happen?

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_jump_to_bottom --list --limit 0 --session <s>`
- Expected: a time column precise enough to order the assertions of one subtest against the child process's markers (ms, as `marker info` elsewhere or `t=471.808s`).
- Got: `t=7m51s`, `t=7m52s` for every row: a whole subtest (click, scroll, wait, fail) fits in the same second, so the list cannot order them. `marker info` prints the same rounded `Time: 7m52s`.
- Workaround: `--json` and read `flatMarkers[].start`.
- What the default output could show: seconds with 3 decimals once the profile is longer than a minute (`t=471.809s` or `7m51.809s`).

## Sandboxed agents: default session dir not writable

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message did say what to do (set `PROFILER_CLI_SESSION_DIR`), which worked, so this cost one call only.
- Note: `profile-link.py` honours the same variable, so it must be exported for it too.

## Question: when did this screenshot start showing? (review-browser_smartwindow_jump_to_bottom.js)

- Command: `profiler-cli screenshots --range 713.52,713.78 -o <dir> --session <s> --json`, then `profiler-cli marker info m-3 --session <s> --json` on one of the handles it returned.
- Expected: one time per screenshot, the same in both.
- Got: `screenshots --json` gives `start` 713539.75 for m-3, and `marker info` gives `start` 713529.54 (end 713546.76) for the same marker. Every screenshot was about 10 ms apart between the two. The text output only prints `t=11m54s`, so it does not settle which one is right.
- Workaround: took the `marker info` interval, which is the one a link opens on.
- What the default output could show: the marker's own start and end, to the millisecond, in the `screenshots` table.
