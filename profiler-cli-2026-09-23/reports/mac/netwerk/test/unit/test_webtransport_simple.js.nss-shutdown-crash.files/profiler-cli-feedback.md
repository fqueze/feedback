## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session ...`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set PROFILER_CLI_SESSION_DIR.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (profile-link.py honours it too). One wasted call; the hint was good.

## C++ warning markers claim a stack that is empty

- Command: `profiler-cli thread markers --search nsAHttpConnection --list` shows `m-144 ... ✓` (has stack); `profiler-cli marker stack m-144`
- Expected: a stack, or no ✓.
- Got: `[1] unknown!null` — a single null frame.
- Workaround: none; treated the marker as stackless.
