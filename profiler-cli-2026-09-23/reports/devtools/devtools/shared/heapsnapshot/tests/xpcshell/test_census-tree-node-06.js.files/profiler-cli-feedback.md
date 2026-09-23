## No way to see test outcomes over time in a resource-usage profile

- Question: "at what point in the job did every test start failing?" (1,152 of 1,552 tests timed out in one xpcshell job).
- Command: `profiler-cli thread markers --session S --search name:test --list --limit 0`, then awk over the text to bucket start times per 10 s and status.
- Expected: something like `--group-by field:status` combined with time buckets (the "over time" view `counter info` has), which would show at once "PASS until 50 s, then only TIMEOUT".
- Workaround: text scraping of the `--list` output (2,843 rows).

## (review) No way to go from a link's `marker=N` to a handle when the marker text is not unique

- Question: "which of the 1,149 identical `not killing -- proc or pid unknown` markers is `marker=11101` in the report's link?"
- Command: `profiler-cli thread markers --search "not killing" --list --limit 0 --json --session S` — the `topMarkers`/list entries carry `handle` and `start` but no `markerIndex`.
- Workaround: pass all 1,149 handles to `marker info … --json` and look for `markerIndex` 11101.
- Would have helped: `markerIndex` in the list JSON, or `marker info --index 11101`.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session S thread markers …`
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand. Accepting it as a global option would match how the briefs phrase "pass --session on every call".
