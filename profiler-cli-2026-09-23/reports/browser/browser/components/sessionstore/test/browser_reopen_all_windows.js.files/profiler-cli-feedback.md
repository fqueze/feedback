## `--session` is rejected before the subcommand (review)

- Command: `profiler-cli --session S thread markers --category Test --search X --list --limit 0`
- Expected: the option accepted anywhere, like most CLIs' global options.
- Got: `error: unknown option '--session' (Did you mean --version?)`
- Workaround: put `--session S` after the subcommand's own options.
