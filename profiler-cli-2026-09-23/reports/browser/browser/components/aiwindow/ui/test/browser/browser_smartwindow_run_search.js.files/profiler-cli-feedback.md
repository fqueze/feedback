# profiler-cli feedback (browser_smartwindow_run_search.js)

## Question: "in what order, to the millisecond, did these log lines happen?"

- Command: `profiler-cli thread markers --session browser-run_search-1 --search browser_smartwindow_run_search --list --limit 0`
  on a 53-minute resource-usage profile.
- Expected: a start time precise enough to order the test's log lines, which are often tens of ms apart
  (here: title generation starts at 2514.239 s, a PASS at 2514.254 s, "Leaving test" at 2514.371 s,
  an FxA warning at 2514.531 s).
- Got: `t=41m52s` for every one of them, so the list cannot order events inside the same second.
- Workaround: `--json` and a python one-liner printing `start/1000` with 3 decimals, for every list I read.
- What would have answered it: print seconds with ms (`t=2514.239s`, or `41m54.239s`) in `--list` mode,
  whatever the profile length.

## (review) Same question again: "in what order, to the millisecond, did these log lines happen?"

- Command: `profiler-cli thread markers --search "FxA token" --list --limit 0 --session <s>` (and 9 other searches) on each of 4 resource-usage profiles.
- Expected: ms start times, to order a worker error against the path-finder line 1 ms later.
- Got: `t=1h21m` for all of them (hour-long profiles round to the minute).
- Workaround: one `marker info <m> --json` call per marker, piped through python for `start` and `markerIndex`. That was about 60 calls.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session <s> zoom push 2511,2541`
- Expected: the global option to be accepted before the subcommand, as with most CLIs.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's arguments.
