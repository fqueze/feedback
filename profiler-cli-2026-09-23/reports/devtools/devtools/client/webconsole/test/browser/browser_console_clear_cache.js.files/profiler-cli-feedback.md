## `--session` is rejected before the subcommand

- Command: `profiler-cli --session review-browser_console_clear_cache.js-1 profile info`
- Expected: the global session option to work in any position, as `--session` is required on every call.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: `profiler-cli profile info --session <id>`.
