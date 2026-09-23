# profiler-cli feedback (browser_ext_runtime_setUninstallURL.js)

## Marker times past one minute are only shown to the second

- Command: `profiler-cli marker info m-6 m-42 m-15 --session browser_ext_runtime_setUninstallURL.js-1` and `thread markers ... --list`
- Expected: a start time precise enough to order markers and build a `zoom push` range, e.g. `t=229.353s`.
- Got: `Time: 3m49s` for markers that are 186 ms apart, and `3m58s` for the failure. In a 4-minute per-test profile, every marker of the test under study prints the same time.
- Workaround: `marker info --json` and read `start` in ms.

## Default session directory is not writable in the sandbox

- Command: `profiler-cli load <url> --session browser_ext_runtime_setUninstallURL.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear suggestion to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, including `profile-link.py`. One wasted call. The message was good.

## Default session directory is not writable in the sandbox (again, in review)

- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_ext_runtime_setUninstallURL.js profiler-cli load <profiler link> --session review-browser_ext_runtime_setUninstallURL.js-1`
- Got: the same `EPERM ... mkdir '/Users/florian/.profiler-cli'`. Each agent pays one wasted call before it sets `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.
- Workaround: the same as above.
