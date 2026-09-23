## Review: `--session` is rejected before the subcommand

- Command: `profiler-cli --session review-bhkn-1 thread markers --category Test --search browser_history_keyboard_navigation --list --limit 0`
- Expected: `--session` taken as a global option, as `load --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` after the subcommand's own options.
