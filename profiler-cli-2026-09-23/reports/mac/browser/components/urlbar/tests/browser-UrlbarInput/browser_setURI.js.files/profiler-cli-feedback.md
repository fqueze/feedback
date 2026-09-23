## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_setURI.js profiler-cli load <taskcluster url> --session browser_setURI.js-1`
- Expected: session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked (`/Users/florian/.sandbox/tmp/profiler-cli`). `profile-link.py` honours the same variable. Cost: one round trip; the hint was good. The subagent brief could mention the variable.
