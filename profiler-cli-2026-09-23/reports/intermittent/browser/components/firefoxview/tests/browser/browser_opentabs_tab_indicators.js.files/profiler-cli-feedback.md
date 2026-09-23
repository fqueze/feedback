## review-browser_opentabs_tab_indicators.js: `--session` before the subcommand is rejected

- Command: `profiler-cli --session <id> thread markers ...`
- Expected: a session option accepted before the subcommand as well, since every command takes it.
- Got: `error: unknown option '--session' (Did you mean --version?)`
- Workaround: put `--session` after the subcommand.
