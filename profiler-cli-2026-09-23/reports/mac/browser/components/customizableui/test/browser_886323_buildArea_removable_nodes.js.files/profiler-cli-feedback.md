## Question: which tests ran before mine in this per-test profile, with their status

- Command: `profiler-cli thread markers --category Test --list --limit 0 --search "name:test"` on the parent main thread.
- Expected: only the per-test `test` markers (`PASS — <path>`, `FAIL — <path>`), one row per test file.
- Got: hundreds of `TEST-PASS`, `INFO` and `BrowserTestUtils` rows too, because `name` is also the payload key of Text markers.
- Workaround: `--json`, then a Python filter on `m['name'] == 'test'`. A way to match the marker name only (e.g. `--name test`, or `marker:test`) would have answered it directly.

## Question: over what time span does one tick reason stay on, and is it continuous for one window?

- Command: `profiler-cli thread markers --search RefreshDriverTick --group-by field:name,field:innerWindowID`.
- Expected: per group, the time span as well as the count, so "this observer is on every tick of window 18 from 18.535 s to the end" can be read off.
- Got: count and duration stats only. I dumped `--list --limit 0 --json` (2371 markers) and wrote a script to find runs of consecutive ticks with the same reason, and the largest gap between them.
- What would have helped: `first`/`last` start times per group in `--group-by` output, and optionally the largest gap between consecutive markers in a group.

## Question (review-browser_886323_buildArea_removable_nodes.js): which window was a `resize - window` DOMEvent fired at?

- Command: `profiler-cli thread markers --search name:DOMEvent --list --limit 0` zoomed to 10.2–19 s.
- Expected: something that tells the main window's resize apart from the one on an in-process tab document, e.g. the innerWindowID or the document URL on the row.
- Got: `resize - window` on every row, the same text for both. The innerWindowID only shows in `rawFields` of `marker info <m-…> --json`, one handle at a time.
- What would have helped: the innerWindowID (or the window's URL) in the `--list` row, or a way to filter on it, e.g. `--search innerWindowID:18`.
- The question already logged above, runs of one tick reason across consecutive ticks, came up again in this review. It took the same `--json` script.
