## `profiler-cli` needs PROFILER_CLI_SESSION_DIR in this sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_fullscreen-popup.js profiler-cli load <taskcluster url> --session browser_fullscreen-popup.js-1`
- Expected: a session in the default directory.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a hint to set `PROFILER_CLI_SESSION_DIR`. One call lost; the hint was good. Worth putting in the brief, since every agent hits it.
- Workaround: a wrapper script setting `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.
