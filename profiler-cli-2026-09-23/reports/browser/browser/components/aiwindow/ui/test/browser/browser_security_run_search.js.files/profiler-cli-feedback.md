## The order of markers a few hundred ms apart, in a long profile

- Question: in which order, to the millisecond, did `ai-window:sidebar-toggle`, `CCSlice` and `ai-window:connected` happen?
- Command: `profiler-cli thread markers --search "eventType:ai-window" --list --limit 0 --session <s>` on a 27-minute per-test profile.
- Expected: start times precise enough to order events 20–300 ms apart (for example `1638.555s`).
- Got: `t=27m13s` for every row. Events within the same second cannot be ordered, and neither can their gaps be measured.
- Workaround: a script over `--json` that prints `flatMarkers[].start / 1000` with 3 decimals (`ml.py` here). I used it for every list in this report.

## How long each CC was, over the whole profile

- Question: how long was each cycle collection, and how did its length and graph size change over time?
- Command: `profiler-cli thread markers --category "GC / CC" --min-duration 20` gives only per-name stats and 3 examples. `--search "name:CC" --list` matches every marker whose name contains "CC" (38,857 rows).
- Expected: a per-`CC`-marker table with start, duration, `mMaxSliceTime`, `mVisitedGCed`.
- Got: aggregated stats, or a flat list without the payload fields.
- Workaround: a script over `--json`, filtering `name == "CC"` and reading `fields` (`cc.py` here). An exact-name match (`name=CC`) would also have helped.
