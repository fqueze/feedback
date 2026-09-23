## `--session` is rejected before the subcommand (review)

- Command: `profiler-cli --session review-atgt-1 thread markers --category Test ...`
- Expected: the global `--session` accepted in any position, like most CLIs' global flags.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand: `profiler-cli thread markers --session review-atgt-1 ...`.
