## At what exact time did each of these markers fire? (list mode rounds to seconds past 1 min)

- Command: `profiler-cli thread markers --search 'name:Runnable' --list --limit 0 --session <s>` after `zoom push 70.4,72.6` (a 2.2 s view).
- Expected: times with ms precision in the flat list, as in the `t=12.671s` format used below one minute, so that events 1-10 ms apart (TCPSocket error, stream copies, `nsIProcess.runAsync`) can be ordered.
- Got: every row printed `t=1m10s`, even inside a 2.2 s zoom. Ordering the adb start-up steps needed `--json | jq '.flatMarkers[] | .start'`.
- Workaround: `--json` and jq over `flatMarkers[].start`.
- What the output could have shown: `t=70.4535s` (or `t=1m10.4535s`) when the view is short, or always three decimals.
