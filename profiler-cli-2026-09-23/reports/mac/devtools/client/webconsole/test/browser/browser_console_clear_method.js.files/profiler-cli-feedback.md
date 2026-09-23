## Default session directory not writable in a sandboxed agent

- Command: `PROFILER_CLI_SESSION_OWNER=browser_console_clear_method.js profiler-cli load <taskcluster url> --session browser_console_clear_method.js-1`
- Expected: session loaded.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error names the fix clearly.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` and `profile-link.py` call (profile-link.py honours it). Cost one round trip; the brief could mention it.
