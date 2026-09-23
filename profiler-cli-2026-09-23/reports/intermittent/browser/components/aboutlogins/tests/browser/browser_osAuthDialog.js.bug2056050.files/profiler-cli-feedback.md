## Marker times past one minute lose sub-second precision in text output
- Command: `profiler-cli thread markers --category Test --search browser_osAuthDialog.js --list --limit 0 --session <s>` (also `marker info m-129`)
- Question: in which order, to the millisecond, did the test's assertions and the `passwordmgr-crypto-login` notification happen?
- Expected: times like `t=62.111s` (or `1m2.111s`).
- Got: every marker after 60 s printed as `t=1m2s`; a whole subtest (15 markers over 150 ms) shows the same time, so the order of events cannot be read. `marker info` prints `Time: 1m2s - 1m2s (513.50μs)`.
- Workaround: `--json` and read `.start` / `.flatMarkers[].start`.

## (review) Which listed marker is a link's `marker=N`?
- Command: `profiler-cli thread markers --thread t-0 --search '<terms>' --list --limit 0 --session <s> --json`, then `profiler-cli marker info <m-…> --json` once per marker
- Question: which of the markers I listed is the one a report's link selects (`marker=732725`)?
- Expected: the list (text or `flatMarkers[]`) to carry each marker's `markerIndex`, or `marker info` to accept an index.
- Got: `markerIndex` only in `marker info --json`, so checking 25 links took a script making about 60 `marker info` calls, one per marker. The same `t=1m2s` precision problem as above also forced `--json` for times.
- Workaround: loop `marker info --json` over the handles.
