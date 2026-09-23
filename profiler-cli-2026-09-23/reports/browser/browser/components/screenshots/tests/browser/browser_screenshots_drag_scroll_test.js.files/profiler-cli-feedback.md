## Exact time of a marker in a long (resource-usage) profile

- Command: `profiler-cli marker info m-33 --session browser-drag_scroll-ru` (20-minute resource-usage profile), and `marker info m-1092..m-1098` for CPU Use markers.
- Expected: a start time precise enough to line up with the per-test profile (ms), e.g. `340.717s`.
- Got: `Time: 5m41s (instant)` and `Time: 5m40s - 5m40s (101ms)`: the minute format drops sub-second precision, so 100 ms CPU Use markers all read `5m40s` and cannot be ordered or matched to an event.
- Workaround: `marker info m-33 --json` and read `start` (340717.252 ms).
- Question the default output could have answered: "when exactly, in seconds, did this marker happen?"

## Search term with spaces does not match the displayed marker label

- Command: `thread markers --list --search "IdleDispatch,...,scroll - document,setTimeout"`.
- Expected: the `DOMEvent  scroll - document` rows, since that is how the list prints them.
- Got: no scroll rows; `--search name:DOMEvent` then grepping for `scroll` found them.
- Question: "list the scroll events on this thread" — the printed label is not searchable as printed.

## Which element got a pointer event in anonymous content (upstream marker payload, not the CLI)

- Command: `profile markers --search pointerdown`.
- Got: `pointerdown - html@10d2083d0` for every event: DOMEvent markers record the retargeted target, so for native-anonymous content (the screenshots overlay) the element actually hit (`mover-topLeft` vs `mover-bottomRight`) is not recorded. Had to derive it from geometry and code.
- Question: "which element did this mousedown hit?" — an `originalTarget` field in the DOMEvent marker payload would answer it.

## Reviewer: both of the first two entries cost me a call again

- `thread markers --search "...,scroll - document,..."` again matched no scroll rows. I needed `--search eventType:scroll`, which the printed label gives no hint of.
- The `CPU Use` rows in the resource-usage profile again all read `5m40s`. I needed `marker info --json` per marker to get the start time and `cpuPercent`. Question: "what was the machine's CPU use over this range?" A list column that shows `cpuPercent` would answer it without a loop.
