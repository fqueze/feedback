## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session <id>`
- Expected: the session starts.
- Got: `Cannot create the profiler-cli session directory /Users/florian/.profiler-cli ... EPERM`. The error was clear and suggested `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every profiler-cli and profile-link.py call. profile-link.py reads the same variable. The skill brief does not mention it, so each agent rediscovers it.
