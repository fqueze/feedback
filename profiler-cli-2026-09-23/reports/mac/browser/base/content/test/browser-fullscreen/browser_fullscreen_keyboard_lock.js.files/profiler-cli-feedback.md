## `load` fails in a sandbox where ~/.profiler-cli is not writable

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session <id>`
- Expected: the session to start, falling back to a writable directory such as `$TMPDIR/profiler-cli`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a suggestion to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (and for `profile-link.py`, which reads the same variable). The hint was clear; one wasted call.
