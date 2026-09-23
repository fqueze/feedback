## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session browser_tab_grouping_telemetry.js-1`
- Expected: the session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session` on the next command.
- Workaround: `export PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the brief does not mention it; other agents on this machine use that directory).

