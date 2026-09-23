## Question: how busy was the machine while one test ran (resource-usage profile)?

- Command: `profiler-cli zoom push 151,218 --session <ru>` then `profiler-cli thread markers --session <ru> --search 'name:CPU Use' --list --limit 0`
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean / min / share of time above 95%), or a sparkline, as `counter info` gives for counters.
- Got: 673 rows with the duration only; the CPU percentage is only visible per marker via `marker info`. `profile info` says "No significant activity" and `counter list` "No counters in this profile", so nothing in the default output says the machine was pinned at 100%.
- Workaround: `--json`, then a Python script averaging `data.cpuPercent` per 5 s bucket.

## (review) A `--list` row printed a whole serialized profile

- Command: `profiler-cli thread markers --search "cores" --list --limit 5 --session <ru>` (resource-usage profile of FN28SmhaRQOc78Qf-Okjrw)
- Expected: one-line rows, with long descriptions cut to the terminal width.
- Got: a `PASS` row from test_TelemetryFeed.js whose message embeds a JSON profile, about 10 KB on one row. The answer (`8 physical, 16 logical cores`) was only in `profile meta`.
- Workaround: `profile meta`, and `| cut -c1-200` on every later `--list`.

## (review) Searching for a `test` marker's own displayed label finds nothing

- Command: `profiler-cli thread markers --search "PASS — xpcshell-remote.toml:toolkit/components/extensions/test/xpcshell/test_ext_permissions.js" --list --session <ru>`
- Expected: the retry's `test` marker, which `--list` displays with exactly that text.
- Got: 0 markers. The displayed text is built from the `status` and `test` fields, so a search for it matches neither field.
- Workaround: search the test path alone, then `zoom push` around the time of the retry.

## (review) Question: which tests were running at time t (resource-usage profile)?

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json --session <ru>`, then a script counting `test` markers that overlap t, by manifest.
- What the output could have shown: at a given time or for a zoomed range, the `test` markers that overlap it, with their count and status. This is the "who shared the machine with this timeout" question.
