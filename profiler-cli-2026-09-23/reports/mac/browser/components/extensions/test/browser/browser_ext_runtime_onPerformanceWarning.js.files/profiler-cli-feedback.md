## Default session directory is not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=… profiler-cli load <taskcluster url> --session …`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggests `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which works.
- Workaround: set `PROFILER_CLI_SESSION_DIR` on every command. It costs one failed call per agent. The brief could say this, or the tool could fall back to a writable directory.
