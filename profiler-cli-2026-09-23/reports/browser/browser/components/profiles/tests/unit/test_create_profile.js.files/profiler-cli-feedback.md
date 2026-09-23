## Question: exact time of a marker after the first minute
- Command: `profiler-cli thread markers --search test_create_profile --list --limit 0`
- Got: `t=2m9s`. Past 60 s the list rounds to whole seconds, which cannot order markers a few ms apart (test end, SharedSurfacesChild warning, hang warning).
- Workaround: `profiler-cli marker info m-163 m-165 ... --json` and reading `start`. In that multi-handle JSON the records also have no `handle` field, so I had to match them by order.

## Question: the machine's CPU use over a time range in a resource-usage profile
- Commands: `profiler-cli zoom push 90,200`, then `thread markers --search "name:CPU Use" --list --limit 0 --json`, and a script to average `cpuPercent` per 2 s bucket.
- The resource-usage profile has no counters (`counter list` is empty), so `counter info` cannot answer it. A per-bucket summary of the CPU Use markers (like `counter info`'s "over time") would have.

## `--session` rejected before the subcommand (review)
- Command: `profiler-cli --session S thread markers --search X --list`
- Expected: a global option accepted anywhere, like `load`'s.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session S` after the subcommand.
