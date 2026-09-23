# profiler-cli feedback

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_label_and_icon.js profiler-cli load <url> --session browser_label_and_icon.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then "Unknown session".
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggests it, which helped).

## Question: "the exact order of a few hundred markers within 2 ms"

- Command: `profiler-cli thread markers --list --limit 0` (after `zoom push 45.270,45.300`)
- Expected: enough time precision to order events that are microseconds apart (a DOMTitleChanged, a FlushTabState runnable, a Glean timing marker).
- Got: times printed as `t=45.284s` (ms resolution); the order had to come from `--json` and a script sorting by `start`.
- Could have shown: sub-millisecond start times in `--list` when the zoomed range is short (e.g. `t=45.28450s`).
