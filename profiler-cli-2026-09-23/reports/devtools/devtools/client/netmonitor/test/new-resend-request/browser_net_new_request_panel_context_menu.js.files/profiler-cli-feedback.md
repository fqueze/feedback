## review-browser_net_new_request_panel_context_menu.js: `--session` before the subcommand

- Command: `profiler-cli --session <id> thread markers --category Test --search ... --list --limit 0`
- Expected: the global-looking `--session` flag accepted anywhere, like `load ... --session`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand (`profiler-cli thread markers --session <id> ...`). An error naming the right position would save the retry.
