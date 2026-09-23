## Question: "how many tests started after time T, by status" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json`, then a Python script splitting the `test` markers at the first launch failure's timestamp and counting the status prefix of their label (`TIMEOUT — <path>`).
- Expected: a way to get this without a script, e.g. a `--started-after <t>` (or `--start-range a,b`) filter that keeps only markers starting in the range, plus `--group-by` on a label prefix. `zoom push` keeps every interval marker overlapping the range, so the long-running `parallel`/`run-tests` markers and the tests started earlier all stay in.
- Got: no filter on start time; the status is only inside the `text` field, so `--group-by field:text` gives one group per test.
- Workaround: the script above.

## Minor: `marker info <handles...> --json` records carry no `handle`

- Command: `profiler-cli marker info m-4 m-6 m-5 --session <s> --json`
- Expected: each record names its handle, so several records can be matched back to the handles asked for.
- Got: `handle` is absent (None); records have to be matched by order.
