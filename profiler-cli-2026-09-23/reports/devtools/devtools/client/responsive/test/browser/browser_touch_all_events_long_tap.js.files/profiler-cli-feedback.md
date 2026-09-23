## `--session` rejected before the subcommand (review-browser_touch_all_events_long_tap.js)

- Command: `profiler-cli --session review-browser_touch_all_events_long_tap.js-1 thread markers --category Test --search browser_touch_all_events_long_tap --list --limit 0`
- Expected: the same as with `--session` after the subcommand; it is a global option every command takes.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` after the subcommand's own arguments.
