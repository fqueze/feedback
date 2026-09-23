## Review: `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-browser_console_open_or_focus.js-1 thread markers --search browser_console_open_or_focus --list --limit 0`
- Expected: `--session` accepted as a global option in any position, like `load ... --session`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's own options.
