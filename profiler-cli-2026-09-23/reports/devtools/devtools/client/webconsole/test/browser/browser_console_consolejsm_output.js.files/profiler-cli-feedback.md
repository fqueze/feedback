## review-browser_console_consolejsm_output.js: `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-consolejsm-1 thread markers --search X --list`
- Expected: `--session` accepted as a global option, as `load ... --session` suggests it is one.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's own arguments.
