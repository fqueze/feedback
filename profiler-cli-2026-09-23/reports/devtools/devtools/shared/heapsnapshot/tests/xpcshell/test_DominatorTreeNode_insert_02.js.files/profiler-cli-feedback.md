# profiler-cli feedback (test_DominatorTreeNode_insert_02.js)

## Question: "at what exact time was this marker, relative to its neighbours?"

- Command: `profiler-cli marker info --session <s> m-2..m-6`
- Expected: a start time precise enough to order markers logged within the same second (ms).
- Got: `Time: 1m38s (instant)` for every marker after t=60 s. The Begin/ERROR/End lines of one
  test's full-log replay are 1 ms apart, and all print as `1m38s`. The `--list` view has the same
  rounding (`t=1m38s`).
- Workaround: `marker info --json` and read `.start` (98172.165, 98173.165, 98176.165).
- Could have shown: `t=98.172s` (or `1m38.172s`) in both `marker info` and `--list`.

## Question: "what was logged in this 4 ms window?"

- Command: `profiler-cli zoom push <s> 98.170,98.178` then `thread markers --list --limit 0`
- Expected: the markers that happened in the window.
- Got: 993 markers, almost all of them 45 s `test` interval markers that merely overlap the window,
  listed first; the ~25 instants logged in the window come after them. `--search` on
  `name:INFO,name:ERROR` still let through `test` markers whose label contains "error".
- Workaround: `--json`, then keep markers whose `start` lies inside the window.
- Could have shown: an option such as `--starting-in-view` (or `--instant-only`) to list only the
  markers that start inside the zoom.
