## Question: which document is this RefreshDriverTick for?

- Command: `profiler-cli thread markers --search 'innerWindowID:4294967338,-name:RefreshDriverTick' --list --session <s>`
- Expected: the other markers of that inner window (its `DocumentLoad`, with the URL), since every `RefreshDriverTick waiting for paint` marker carries only an innerWindowID.
- Got: `0 markers`, although `DocumentLoad` and `nsRefreshDriver` markers in that thread do carry `innerWindowID: 4294967338` (shown under `rawFields` / `data` in `--json`).
- Workaround: `thread markers --search RefreshDriverTick --list --limit 0 --json` dumped to a file, and a Python script grouping by `data.innerWindowID`, then `marker info --json` on the `DocumentLoad` markers to match the IDs.
- What would have shown it: `--search innerWindowID:<id>` matching raw fields, or the marker list printing the page URL of a marker's innerWindowID (the profile's `pages` table has it).
## Question: is the parent's tick before or after the wait started? (review-browser_ext_history_redirect.js)

- Command: `profiler-cli zoom push 111.15,120.0` then `profiler-cli profile markers --search RefreshDriverTick --session <s>`, and `thread markers --search waitForCondition --list`
- Expected: start times precise enough to order markers that are milliseconds apart.
- Got: every time past 60 s printed as `t=1m51s`, for the parent's two ticks and for the wait's start alike, so the list could not say whether the parent ticked inside the wait.
- Workaround: `--json` and a script printing `start/1000` to 4 decimals (111.1539 wait start, 111.1564 and 111.1734 ticks).
- What would have shown it: `t=111.156s` (or `1m51.156s`) in list rows, as below 60 s.

## Question: which threads, and how many markers each, match X in this range? (review-browser_ext_history_redirect.js)

- Command: `profiler-cli profile markers --search 'PVsync::Msg_Notify' --session <s>` inside a zoom
- Expected: a per-thread count ("t-21: 531, t-16: 1, t-0: 532 IPCOut"), to see which content processes still receive vsync during the wait.
- Got: a header "N markers across K of 22 threads" and the rows one by one; the per-thread split is only in `--json` (`byThread`).
- Workaround: `--json` and a Counter over `threadHandle`.
- What would have shown it: the `byThread` summary printed above the rows.
