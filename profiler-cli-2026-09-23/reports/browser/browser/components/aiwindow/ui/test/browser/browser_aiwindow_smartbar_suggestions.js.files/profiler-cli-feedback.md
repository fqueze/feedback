## Times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --category Test --search browser_aiwindow_smartbar_suggestions --list --limit 0 --session <s>` and `profiler-cli marker info m-16 --session <s>` on a 520 s profile (task PhB0HyMnTrSYjaqQYIFWhg, mac debug).
- Expected: marker times in seconds with milliseconds, as for profiles under a minute (`t=30.339s`), so they can be passed to `zoom push` and compared with other markers.
- Got: `t=8m39s` / `Time: 8m39s (instant)` for every marker in that minute: several test steps, the failure and the helper waits all print the same time, and none can be ordered or zoomed to.
- Workaround: `marker info m-16 --json` and read `start` (519042.43 ms).

## Which code dirtied style in a range (review)

- Question: which of ~100 `SetNeedStyleFlush` markers in 30.05–30.70 s came from `UrlbarView` code, such as `#setRowVisibility` or `#updateResults`?
- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>` lists handles only. `marker stack` takes one handle, unlike `marker info m-a..m-b`, and `thread markers` cannot filter on a stack frame.
- Workaround: a shell loop running `marker stack <h>` once per handle and keeping its top 6 frames. That took about 100 calls per profile.
- What would have answered it: the leaf JS frame as a column in `--list` for markers with stacks, a `--stack-search <function>` filter, or `marker stack m-a..m-b`.
- I also hit "Times past one minute lose their milliseconds" (above) on PhB0HyMnTrSYjaqQYIFWhg, with the same `--json` workaround.
