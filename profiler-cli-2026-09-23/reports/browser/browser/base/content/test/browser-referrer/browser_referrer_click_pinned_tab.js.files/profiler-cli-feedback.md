
## review-browser_referrer_click_pinned_tab.js, 2026-09-22 — `--session` before the subcommand is rejected

- Command: `profiler-cli --session browser-review-browser_referrer_click_pinned_tab.js-1 profile info --all`
- Expected: the session applies to the command, as it would for a global option.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand (`profiler-cli profile info --all --session <id>`). Accepting it in either position, or suggesting the right place in the error, would save a retry.
