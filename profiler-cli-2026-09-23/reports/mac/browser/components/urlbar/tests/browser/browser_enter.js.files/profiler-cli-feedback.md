## Question: at what millisecond did each step of the test log happen, relative to the DOM events and actor messages around it?

- Command: `profiler-cli thread markers --category Test --search browser_enter.js --list --limit 0 --session browser_enter.js-1` (and the same `--list` for `DOMEvent` and `JSActorMessage` markers, even inside a 250 ms `zoom push`)
- Expected: a time column precise enough to order markers that are milliseconds apart (e.g. `t=63.2123s`).
- Got: every row printed `t=1m3s`, because past one minute the time is rounded to whole seconds. A 250 ms race across ~40 markers was unreadable.
- Workaround: `--json | jq -r '.flatMarkers[] | "\(.handle) \(.start) \(.name) \(.label)"'` for every list.
- What the default output could show: milliseconds (or `1m3.212s`) in `--list` rows, at least when zoomed into a sub-second range.

## Session directory not writable in the sandbox

- Command: `profiler-cli load <taskcluster url> --session browser_enter.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (and for profile-link.py).
