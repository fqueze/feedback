## Marker list times lose sub-second precision past one minute

- Command: `profiler-cli thread markers --session browser_toolbar_sidebar_button.js-1 --category Test --search test_states_for_hide_sidebar_vertical --list --limit 0`, and the same with `--search DOMEvent` after `zoom push 140.70,140.90`.
- Question: at what time did each step of the test run, and did the click come before or after the `INFO` that precedes it?
- Expected: a time column precise enough to order and zoom on, e.g. `t=140.752s`.
- Got: every row reads `t=2m21s`, 40+ test-log rows and 180+ DOMEvent rows alike; `marker info` without `--json` also prints `Time: 2m21s (instant)`. Nothing in the default output distinguishes a 200 ms window.
- Workaround: `profiler-cli marker info m-28 m-17 --json` and a script to read `.start` in ms.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session browser_toolbar_sidebar_button.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message named the fix (`PROFILER_CLI_SESSION_DIR`), so this cost one retry only.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command.
