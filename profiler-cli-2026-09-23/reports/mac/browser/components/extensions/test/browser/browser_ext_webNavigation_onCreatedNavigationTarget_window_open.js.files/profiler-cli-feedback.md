## Question: "was the machine saturated while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-402 --session <s>; profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0`
- Expected: each `CPU Use` row showing its `cpuPercent`, or a one-line summary (avg/max CPU%) for the zoomed range.
- Got: rows with only name, time and duration; the CPU percentage is only in `--json` (`data.cpuPercent`), so I had to script an average over 130 markers.
- Workaround: `--json | python3` averaging `data.cpuPercent`.
- What would have answered it: the CPU% in the list rows, or a `CPU Use` aggregate (avg/max) in the non-list `thread markers` summary for the view.

## `profile markers --search` on a large per-test profile exceeds 120 s

- Command: `profiler-cli profile markers --session <s> --search Vsync` on a per-test profile with 1.2M markers on the parent main thread.
- Expected: a few seconds.
- Got: still running after 2 minutes (moved to background).
- Workaround: per-thread `thread markers --search`.

## (review) Socket-path error suggests the directory already in use

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <url> --session review-browser_ext_webNavigation_onCreatedNavigationTarget_window_open.js-2`
- Expected: an error saying the session name is too long, and how long it can be.
- Got: "The Unix socket path for this session is 121 bytes, over this platform's 103-byte limit", followed by the suggestion `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which is the directory already set. That fix cannot work.
- Workaround: a shorter session name (`review-webnav-window_open-2`).
- What would have helped: when the directory is already set, suggest a shorter `--session`, and say the maximum name length for that directory.
