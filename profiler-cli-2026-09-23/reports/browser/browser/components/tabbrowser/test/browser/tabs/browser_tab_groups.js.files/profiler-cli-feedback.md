## Question: "at what millisecond did each of these test log lines happen?"

- Command: `profiler-cli thread markers --session <s> --thread t-0 --category Test --search browser_tab_groups.js --list --limit 0`
- Expected: timestamps that tell apart markers a few ms apart (the whole failing subtest ran in 10 ms).
- Got: every row shows `t=3m21s`; the ordering is there but not the gaps, which are the evidence in a race.
- Workaround: `--json` and print `flatMarkers[].start` with a Python one-liner, for every marker listing in this investigation.
- What the default output could have shown: ms (or sub-ms) resolution once the listed markers span less than a few seconds, or relative to the first listed marker.

## Question: "which markers in this range have a stack through function X?"

- Command: none exists; `thread markers --search` matches names and payloads, not stack frames.
- Expected: e.g. `thread markers --has-stack --stack-search scrollIntoView` to find the `SetNeedStyleFlush` / `DoFlushPendingNotifications` markers caused by `arrowscrollbox.js!#updateScrollButtonsDisabledState` or `Element.scrollIntoView`.
- Got: had to list ~200 stacked markers with `--json`, then call `profiler-cli marker stack <m>` once per marker in a shell loop and grep the frames (several minutes per profile).
- What the default output could have shown: a stack-frame filter on `thread markers`, or the top JS frame of each stacked marker in `--list` output.
