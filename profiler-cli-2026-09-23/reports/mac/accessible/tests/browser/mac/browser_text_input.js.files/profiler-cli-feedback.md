## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_text_input.js profiler-cli load "<taskcluster profile URL>" --session browser_text_input.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a suggestion to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `export PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (other agents already use that directory). The brief does not mention it, and `profile-link.py` needs the same variable to find the session.

## `profile-link.py --check` prints only the URL

- Command: `python3 profile-link.py --session browser_text_input.js-3 --marker m-807 --check`
- Expected: a line saying which thread and marker the link opens on, per `--help` ("load the link back to check where it opens").
- Got: the URL alone, the same output as without `--check`, so I cannot tell whether the check ran or passed.
- Workaround: none; trusted the link.
