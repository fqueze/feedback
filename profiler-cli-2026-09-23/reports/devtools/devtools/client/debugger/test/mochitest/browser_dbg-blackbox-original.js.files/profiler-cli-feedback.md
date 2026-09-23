## Question: in what order, and how far apart, did these markers happen (after the first minute)?

- Command: `profiler-cli thread markers --session S --search name:DOMEvent --list --limit 0` (also `--category Test --list`)
- Expected: timestamps precise enough to order events that are milliseconds apart, e.g. `t=128.801s`.
- Got: every marker past 60 s prints as `t=2m9s`. The test log line, the mousedown, the flush and the click all read `t=2m9s`, 13 ms apart.
- Workaround: `marker info m-N --json` for each marker, or `--list --json` piped through a script, reading `.start`.

## Question: which document did each Reflow / DoFlushPendingNotifications lay out?

- Command: `profiler-cli thread markers --session S --search name:Reflow --list --limit 0`
- Expected: the marker's `innerWindowID` (better, the document URL) in the row, and a way to keep only one document's reflows.
- Got: no document column. `--search "name:Reflow,innerWindowID:2"` is an OR, so it returned every Reflow plus every marker of window 2. There is no AND.
- Workaround: `--list --json`, then a script filtering `.data.innerWindowID == 2`.
