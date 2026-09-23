## Question: "at which Date.now() millisecond did this marker happen?"

- Command: `profiler-cli marker info m-25 m-455 m-481 m-4 --session test_ext_downloads_search.js-1` (and `thread markers --list`)
- Expected: a sub-millisecond time, and optionally the absolute wall-clock time (profile `meta.startTime` + `profilingStartTime` + marker time), so two instants can be checked against JS `Date`'s 1 ms resolution.
- Got: `Time: 2.765s (instant)` for all four markers, which were 0.8 ms apart; the list shows `t=2.765s` too. Nothing absolute.
- Workaround: `marker info --json` for `start` (2765.391), plus `profile meta --json` `startTime` to compute the epoch by hand; then a Python script over the downloaded profile JSON to build the same table for six profiles.
- What would have answered it: `Time: 2765.391ms (wall clock 1790014238929.228)` in `marker info`, and more digits in `--list` when neighbouring markers share the rounded value.

## Question: "how long did the same steps take in each of these failing runs?"

- Command: none fit; I compared one sequence of markers (a TEST-PASS, an ExtensionParent api_call, an Event::record, a Network Load) across six profiles.
- Workaround: downloaded each profile and ran a script over the raw JSON (`gaps.py` in this directory).
- What would have answered it: `thread markers --search a,b,c --list` with a column showing the delta from the previous listed marker.

## Question: "which markers ran between these two instants, 0.8 ms apart, and in what order?" (review)

- Command: `profiler-cli zoom push 2.7653,2.7663 --session review-test_ext_downloads_search.js-1` then `thread markers --list --limit 0`
- Expected: each row's start at µs resolution, so the order of an `Event::record`, a `SendAsyncMessage [Conduits] CallResult` and a `Load` can be read off.
- Got: `t=2.765s` / `t=2.766s` on all 103 rows. Interval markers spanning the whole window also come first, so reading the order meant sorting the rows by hand.
- Workaround: `--json`, then a script sorting `flatMarkers` by `start` (`review/KJ7-window.json`).
- What would have answered it: times printed with enough digits for the zoomed range (e.g. `t=2766.151ms` once the range is under 10 ms).
