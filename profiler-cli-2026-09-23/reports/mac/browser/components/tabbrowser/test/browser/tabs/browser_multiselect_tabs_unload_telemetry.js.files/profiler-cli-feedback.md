## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_multiselect_tabs_unload_telemetry.js-1`
- Expected: a loaded session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a suggestion to set PROFILER_CLI_SESSION_DIR.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (and profile-link.py reads the same variable). The error message was clear; the cost was one retry. The subagent brief does not mention the variable.

## Marker times lose millisecond precision past one minute

- Question: in which order did a process launch completion, a TabSelect and two GetProcInfo resolutions happen, a few ms apart, 2m44s into a per-test profile?
- Command: `profiler-cli thread markers --search '...' --list --limit 0 --session browser_multiselect_tabs_unload_telemetry.js-2`
- Expected: `t=164.525s` style times, as short profiles get (`t=3.897s`).
- Got: `t=2m45s` for every row, so ten markers spanning 30 ms print the same time and the order that decides the diagnosis cannot be read. `zoom push` did not change the format.
- Workaround: `--json` and a Python script printing `start/1000` with three decimals.
- What the output could show: seconds with ms precision (or `2m44.525s`) whenever the rows listed span less than a few seconds.
