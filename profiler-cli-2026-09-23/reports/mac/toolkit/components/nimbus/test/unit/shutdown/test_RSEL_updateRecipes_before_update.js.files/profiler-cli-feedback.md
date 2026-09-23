## review-test_RSEL_updateRecipes_before_update.js: `--session` rejected before the subcommand

- Command: `profiler-cli --session review-RSEL-1 thread markers --search "Timer.sys.mjs" --list --limit 20`
- Expected: the global `--session` option to work in any position, as `load ... --session` suggests it is global.
- Got: `error: unknown option '--session' (Did you mean --version?)`. The suggestion sends you the wrong way.
- Workaround: put `--session <id>` after the subcommand's own arguments.
