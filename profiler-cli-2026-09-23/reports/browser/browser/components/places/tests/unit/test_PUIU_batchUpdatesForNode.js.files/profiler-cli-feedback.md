## Times past 60 s lose their sub-second part

- Command: `profiler-cli marker info m-11 m-13 --session …-3` (Xie8B resource profile)
- Expected: `Time: 59.848s - 1m44.856s` and `Time: 59.855s` (and in `thread markers --list`, `t=59.848s`).
- Got: `Time: 1m - 1m45s (45.008s)` and `Time: 1m (instant)`: 59.848 s is rounded to "1m". The 7 ms gap between TEST-START and "will retry" was exactly the evidence needed, and it was invisible.
- Workaround: `marker info … --json` and reading `start`.

## (review) `--session` is rejected before the subcommand

- Command: `profiler-cli --session <id> thread markers --search …`
- Expected: a global option that is accepted anywhere, as in `git -C`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: `profiler-cli thread markers --session <id> …`.

## (review) Question: "how many tests passed, failed or timed out before and after time T"

- Command: `profiler-cli thread markers --session … --search name:test --list --limit 0 --json`, plus a Python script that buckets `data.status` by `start`.
- Expected: something like `thread markers --search name:test --group-by status` with a zoom, or a `--before/--after <t>` split. That would count statuses directly.
- Got: 673 rows with no aggregation by payload field value, so I had to script it (both the author and the reviewer did).
