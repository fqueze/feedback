## Default session directory not writable under the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session <id>`.
- Expected: the session to start.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked (and `profile-link.py` honours it). One round trip; the hint was good. Other sessions already lived in that directory, so falling back to `$TMPDIR/profiler-cli` when the default is not writable would have avoided it.
