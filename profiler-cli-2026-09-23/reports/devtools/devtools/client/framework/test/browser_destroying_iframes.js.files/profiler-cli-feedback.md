## Question: where does `--session` go (review-browser_destroying_iframes.js)

- Command: `profiler-cli --session <id> thread markers --search browser_destroying_iframes --list --limit 0`
- Expected: `--session` accepted as a global option before the subcommand, the way `--version` is.
- Got: `error: unknown option '--session' (Did you mean --version?)`. The suggestion points the wrong way. The option only works after the subcommand.
- Workaround: moved `--session` to the end of the command.
- What the output could have shown: accept it in either position, or say "`--session` goes after the subcommand".

## Question: this test's own log lines in a resource-usage profile, without LSan output

- Command: `profiler-cli thread markers --search browser_destroying_iframes --list --limit 0 --session <id>` on an ASan resource-usage profile
- Expected: the test's log lines and console messages.
- Got: 91.5 KB, mostly one `output` marker per line of the LSan "Suppressions used" table, all tagged with the test name.
- Workaround: `--search browser_destroying_iframes,-name:output`.
- What the output could have shown: collapse runs of consecutive `output` markers into one row with a count.
