## Question (review): which tests ran in this browser, with their status?

- Question: the `test` markers only (one per test, with status) in a time range of a resource-usage profile.
- Command: `profiler-cli thread markers --session <id> --search "name:test" --list --limit 50`
- Expected: the `test` markers.
- Got: 8,705 hits, mostly `Runnable` "AsyncExecuteStatements::…" (substring "teSt"), as the help says `name:` is a substring match. `--search "TIMEOUT —"` matched nothing either, since the row text is not searchable.
- Workaround: `--search <test path>` and keep rows whose name column is `test` with `rg`.
- What the output could have shown: an exact-name filter (e.g. `name:=test`), or a `--name` option.
