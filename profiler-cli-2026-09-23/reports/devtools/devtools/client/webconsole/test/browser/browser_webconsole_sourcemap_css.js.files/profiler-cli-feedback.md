## Question: in what order, to the sub-millisecond, did a worker thread's markers run?

- Command: `profiler-cli thread markers --session <s> --list --limit 0` (thread t-90, a DOM Worker, zoomed to 121–128 s), and `profiler-cli marker info m-2499 m-3472`.
- Expected: start times precise enough to order markers that are microseconds apart, e.g. `t=122.15649s`.
- Got: past one minute, times print as `t=2m2s`, so every marker of the 5 s window reads `t=2m2s`, and `marker info` prints `Time: 2m2s - 2m2s (711.66μs)`. I could not tell whether `ReleaseRefControlRunnable` ran before `ContinueConsumeBodyRunnable`, or what came after the fetch.
- Workaround: `--json` and a Python script that prints `start/1000` with 5 decimals. Useful output would keep millisecond (or finer) precision past 60 s, e.g. `t=2m2.15649s` or `t=122.156s`, at least in `--list`.

## Question: which session does this command go to?

- Command: `profiler-cli --session <s> thread markers …`
- Expected: the global-looking option to be accepted before the subcommand.
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand (`profiler-cli thread markers --session <s> …`).
- Workaround: moved it. Accepting it in either place, or an error that names the right place, would have saved the retry.
