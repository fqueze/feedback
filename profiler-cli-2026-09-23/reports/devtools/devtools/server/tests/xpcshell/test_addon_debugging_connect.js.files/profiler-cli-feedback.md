## Question: how busy was the machine while this one test ran? (resource-usage profile)

- Command: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "CPU Use" --list --limit 0`
- Expected: each row showing its CPU percentage (and idle %), so the list answers "was the machine saturated during the test?".
- Got: rows with a start time and a duration only; the CPU values are in the payload. `counter list` says "No counters in this profile", and `profile info` says "No significant activity", which reads as "the machine was idle" when it was at 100%.
- Workaround: `thread markers --search "CPU Use" --list --limit 0 --json` piped through a Python script printing `data.cpuPercent` and `data.idle_pct` per marker. The default `--list` line could show `CPU Percent` and `Idle %`, as the Test markers show their message.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-…-1 thread list`
- Expected: the global option applies, as with most CLIs.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand (`profiler-cli thread list --session …`). Either accept it globally, or say "--session goes after the subcommand" in the error.
