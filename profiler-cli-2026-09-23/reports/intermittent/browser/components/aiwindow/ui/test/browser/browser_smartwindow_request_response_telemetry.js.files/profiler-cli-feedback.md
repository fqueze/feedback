## Question: the exact time of a marker

- Command: `profiler-cli thread markers --category Test --search <file> --list --limit 0` and `profiler-cli marker info m-N`
- Expected: millisecond timestamps; the diagnosis was a race with a margin of ~1 s between markers all shown as `t=6m49s`.
- Got: times rounded to the second in both `--list` and `marker info`.
- Workaround: `marker info m-a m-b ... --json` and a script to print `start`/`end`.

## Default load timeout too short for large CI per-test profiles

- Command: `profiler-cli load <taskcluster url of profile_browser_smartwindow_tab_switching_starters.js.json> --session X`
- Expected: the load to finish (it did, after ~2m45s, with symbolication).
- Got: `Error: Profile load timeout after 60000ms`, then `Profile still loading` for every command.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=500000`, and polling `thread select` until ready.

## Field search on network marker payloads matched nothing

- Command: `profiler-cli thread markers --search "requestStatus:NS_ERROR_CONNECTION_REFUSED" --list --limit 0 --json`
- Expected: the `Load` markers whose `requestStatus` is `NS_ERROR_CONNECTION_REFUSED` (three exist; `marker info` shows the field).
- Got: no markers.
- Workaround: `thread network --search localhost` and grepping for `???`/`canceled`.

## Question: when did this network request start and end, on the same clock as the other markers?

- Command: `profiler-cli marker info m-1560 --session review-rrt-1` (P1, `Load 2592`) and `--json`
- Expected: one set of times, comparable with `NotifyObservers` and `SmartWindow` markers.
- Got: marker `start`/`end` 407593.12 → 408536.80, and under "Fields (raw payload, no schema)" `startTime`/`endTime` 407600.54 → 408544.22: a constant +7.41 ms in this profile (+7.12 ms in its `-2`), on every network marker, with no hint that the payload fields are on another clock. The report under review quoted the payload times next to marker times, which made `Load 2592` seem to end 2 ms after the `ServerE2E` marker that its own completion closes, and a request "open 9 ms" after its start marker when it was 1.5 ms.
- Workaround: compare `start`/`end` only; the payload is what the output leads you to quote.
- Could have shown: the network phases (`domainLookupStart`, `connectStart`, ...) converted to the marker clock, or labelled as an unadjusted clock.

## `zoom push` accepts a range past the end of the profile

- Command: `profiler-cli zoom push 399800,399910 --session review-rrt-1` (meant ms; the profile is 6m53s)
- Expected: an error, since 399800 s is past the end.
- Got: `View: ts>10w→ts>10W (1m50s)`, then "0 markers in view" with no explanation.
- Workaround: seconds, `zoom push 399.800,399.910`.
