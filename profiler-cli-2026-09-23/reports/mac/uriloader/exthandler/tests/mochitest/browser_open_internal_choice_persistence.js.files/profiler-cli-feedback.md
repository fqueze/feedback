## Default session directory not writable in a sandboxed agent

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session browser_open_internal_choice_persistence.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, as the error message suggests. One wasted call; the message was clear.

## `--session` rejected before the subcommand (review)

- Command: `profiler-cli --session review-boicp-1 thread markers --category Test --search <file> --list --limit 0`
- Expected: the global `--session` option accepted in any position, like `git -C`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand. One wasted call; the suggestion of `--version` misleads.
