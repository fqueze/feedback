## `--session` before the subcommand is rejected (review-browser_privatebrowsing_about_default_pin_promo.js, 2026-09-22)

- Command: `profiler-cli --session rv-pinpromo-1 thread markers --search PBNEWTAB_MESSAGE_REQUEST --list --limit 0`
- Expected: `--session` accepted as a global option anywhere, as `load ... --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` after the subcommand's own arguments.
