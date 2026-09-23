## Question: when, to the millisecond, did each line of the test's log happen?

- Command: `profiler-cli thread markers --category Test --search <test file> --list --limit 0 --session <s>` and `profiler-cli marker info m-8 m-16 m-19 ... --session <s>` on a 77 s per-test profile (task Vf2fMbEOQiiiZE3hM04h3w).
- Expected: times precise enough to order the test's lines against runnables (ms, as `t=76.8635s`).
- Got: every marker printed as `t=1m17s`, in both `--list` and `marker info` (`Time: 1m17s (instant)`, `Captured at: 1m17s`). The 64 ms toolbox open and the 3 ms between "Toolbox opened" and the failure were invisible.
- Workaround: `--json` and read `start` / `duration` / `stack.capturedAt` with a python one-liner. The default output could print seconds with 3-4 decimals once past a minute (`t=1m16.8635s`).
- Minor, same session: after `zoom push m-35`, `marker info --json` reports `context.currentViewRange.start` 8.19 ms (= `rootRange.start`) later than the marker's own `start`, so the two JSON fields use different origins.

## Review (review-browser_toolbox_keyboard_navigation_notification_box.js): the same question, the same cost

- Command: `profiler-cli zoom push 76.795,76.95` then `thread markers --search ... --list --limit 0`, on the same profile.
- Got: every row still showed `t=1m17s`, even inside a 155 ms zoom. The text output could not order 6 markers. `marker stack` prints `Captured at: 2m40s`. The ms value is only in `marker info --json` as `.stack.capturedAt`, and `marker stack --json` does not include it.
- Workaround: the same `--json` plus a python one-liner, on every listing.
