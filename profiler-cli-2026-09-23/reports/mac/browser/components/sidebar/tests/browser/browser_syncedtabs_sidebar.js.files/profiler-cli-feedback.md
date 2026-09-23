## Ordering markers within one second

- Question: in which order did the test's `INFO` log line, the start of the `waitForMutationCondition` interval marker, and the tab list render happen, all within the same second?
- Command: `profiler-cli thread markers --category Test --search browser_syncedtabs_sidebar.js --list --limit 0 --session <s>` and `profiler-cli marker info m-16 --session <s>`
- Expected: times to the millisecond (e.g. `t=94.501s`), as the guide's own examples show (`t=12.671s`).
- Got: `t=1m35s` / `Time: 1m35s - 1m45s (10.070s)` for every marker in that second, so the order of events within it cannot be read.
- Workaround: `marker info ... --json` and reading `start`.

## Which document's style changes fall between two points a few ms apart, and from what (review)

- Question: which `SetNeedStyleFlush` markers of the sidebar document fall between a wait's first check (94500.93 ms) and its timeout, and what stack each has.
- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>` inside a `zoom push`
- Expected: per row, a millisecond time, the innerWindowID (or document URL), and the stack's top frames, so insertions (`Node.insertBefore`, `ContentRangeInserted`) can be told from `ElementStateChanged`.
- Got: 171 identical rows `SetNeedStyleFlush t=1m34s instant ✓`: no ms time, no window, no stack hint.
- Workaround: `--json` for `start` and `data.innerWindowID`, then `marker stack` once per handle in a shell loop (57 calls).
