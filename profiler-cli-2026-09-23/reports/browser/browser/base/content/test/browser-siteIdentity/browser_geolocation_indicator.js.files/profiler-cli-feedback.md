## `--session` rejected before the subcommand (review of browser_geolocation_indicator.js)

- Command: `profiler-cli --session browser-review-browser_geolocation_indicator.js-1 thread markers --search X --list`
- Expected: the global option accepted in front of the subcommand, as `profiler-cli load ... --session` suggests it is session-wide.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand (`profiler-cli thread markers --session ...`).

## IPCIn/IPCOut rows in `thread markers --list` have no description (same review)

- Question: which IPC message is each `IPCIn` / `IPCOut` row matched by `--search UntrustedModulesData`?
- Command: `profiler-cli thread markers --session <s> --search "UntrustedModulesData" --list --limit 0`
- Got: 20 `IPCIn`/`IPCOut` rows with an empty description column (some with negative durations such as `-11,181,786ns`), so the search matched on a payload field that is not shown.
- Could have shown: the message type and direction (e.g. `PContent::Msg_GetUntrustedModulesData → pid N`) in the description column, as the `Runnable` rows do.
