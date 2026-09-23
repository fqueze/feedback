## review-WebExtensionTest.kt.bug2064852: `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-WebExtensionTest.kt.bug2064852-1 thread markers --search testBrowserTabsRemove`
- Expected: `--session` to work as a global option, since every subcommand takes it.
- Got: `error: unknown option '--session' (Did you mean --version?)`. The suggestion points away from the fix.
- Workaround: put `--session` after the subcommand (`profiler-cli thread markers --session … --search …`).
