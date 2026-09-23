## Times past one minute are rounded to the second in text output (`profile markers`, `thread markers --list`, `marker info`)

- Command: `profiler-cli profile markers --search DOMPopupBlocked --session <s>`
- Expected: `t=73.228s` (or `1m13.228s`). The question was "which iframe blocked its popups first, and how many ms apart?"
- Got: `t=1m13s` for all four markers of one process and `t=1m15s` for the other's, which can't be ordered. `marker info` prints `Time: 1m15s - 1m15s (41.422ms)`.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals, which I needed on every marker list in this investigation.

## (review) Same issue as above, hit again while checking this report

- Command: `profiler-cli thread markers --search "GetBlockedPopups,UpdateBlockedPopups,..." --list --limit 0 --session <s>`
- Question: "did the second iframe's `UpdateBlockedPopups` reach the parent before or after the `GetBlockedPopups` reply?" (74.828 vs 74.837 s). The text output prints both as `t=1m15s`.
- Workaround: `--json` plus a formatter again.
