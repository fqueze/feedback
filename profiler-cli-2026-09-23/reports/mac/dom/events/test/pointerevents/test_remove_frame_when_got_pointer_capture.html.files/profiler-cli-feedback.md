## `load` fails in a sandbox where `~/.profiler-cli` is not writable

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session <name>`
- Expected: the load to work, or a fallback to `$TMPDIR`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by a second error: `Unknown session ...: no metadata found`. The message does say to set `PROFILER_CLI_SESSION_DIR`, which helped.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Falling back to `$TMPDIR` when the default directory cannot be created would avoid a wasted round trip for every sandboxed agent.
