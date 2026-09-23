## Question: which document is innerWindowID N?

- DOMEvent markers name targets as `window`, `document` or `html@<addr>` with an `innerWindowID`; telling the test window, the two windows it opens, and the other top-level windows apart needed guessing from load/focus markers of each ID.
- Commands tried: `profiler-cli profile meta --json` (no `pages`), `thread markers --search innerWindowID:90` (0 markers, the field is only in `rawFields`).
- What could have shown it: the profile's `pages` table (innerWindowID -> URL, embedder) in `profile info` or `profile meta`, and the URL next to the innerWindowID in `marker info`.

## Cannot select DOMEvents whose event type is empty

- Internal events such as `eMouseEnterIntoWidget` produce DOMEvent markers with `eventType` empty (listed as ` - html@...`).
- Command: `thread markers --search "eventType:"` matches nothing, so they only show when listing every DOMEvent.
- Workaround: `--search name:DOMEvent` inside a narrow zoom, then reading all of them.

## Question: which DOMEvents came from the same native event, and how old was it? (review-test_bug596600.xhtml)

- Several empty-type DOMEvents fire within microseconds; telling a queued NSEvent (old timestamp, shared by its exit/enter pair) from a focus-change re-evaluation (timestamp = now) needs `start - latency` per marker.
- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0` shows neither latency nor innerWindowID in the rows; `marker info` shows them one record at a time.
- Workaround: `--list --json`, then a Python script over `flatMarkers[].data.latency` and `.innerWindowID` to print `start - latency`.
- What could have shown it: for DOMEvent rows in `--list`, the latency (or the event's own timestamp) and innerWindowID as columns.
