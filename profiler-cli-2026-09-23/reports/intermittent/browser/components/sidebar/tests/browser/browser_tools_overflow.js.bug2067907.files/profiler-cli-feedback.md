## Question: "at what exact time did this test-log marker happen?"

- Command: `profiler-cli thread markers --category Test --search browser_tools_overflow.js --list --limit 0 --session ...`
- Expected: sub-second timestamps, to pick screenshot/zoom times around a step (steps were 0.2-0.4 s apart).
- Got: `t=1m9s` / `t=1m10s` (1 s resolution) for every row.
- Workaround: `marker info m-A m-B --json` and read `.start` (ms).
- Could have shown: `t=69.634s` (or ms) in the list view, at least when a zoom/range is shorter than a few minutes.

## Question: "which script dirtied style between two test steps?" (review-browser_tools_overflow.js.bug2067907)

- Command: `profiler-cli zoom push 146.12,146.53; profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session ...`
- Expected: per row, the code that caused the flush (e.g. `set launcherExpanded`, `_toolsIntersectionObserver` callback).
- Got: 138 rows that each say only `SetNeedStyleFlush ✓`, with no stack text.
- Workaround: `marker info m-1..m-138 --json`, then a script over `.stack.frames` to print the top frames and filter on function names.
- Could have shown: the innermost 2–3 frames of each row's stack, or a way to filter markers by a function in their stack (like `--search` on the samples commands).
