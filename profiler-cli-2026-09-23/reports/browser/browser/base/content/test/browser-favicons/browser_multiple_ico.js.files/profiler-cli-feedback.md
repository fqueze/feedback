# profiler-cli feedback (browser_multiple_ico.js)

## List times are too coarse to order events that are milliseconds apart

- Command: `profiler-cli thread markers --search "Link:SetIcon,TabAttrModified,openNewForegroundTab,browserLoaded" --list --limit 0 --session <s>`
- Expected: start times precise enough to tell which of two markers came first (these were 0.6 to 6 ms apart).
- Got: every row shows `t=1m3s`. The race was invisible in the default output.
- Workaround: `marker info m-a m-b ... --json` and `thread markers --list --json`, then a Python script to print `start` and `start+duration` in ms.
- Question it could have answered: "in what order, to the millisecond, did these markers happen?" A `--precise` flag, or ms resolution once the view (or zoom) is under a few seconds, would do.

## "How long after its load did each openNewForegroundTab resolve?"

- Command: `thread markers --category Test --search "openNewForegroundTab,browserLoaded" --list --limit 0 --json`, then a script that matches each `browserLoaded` to the `openNewForegroundTab` that contains it and prints end minus end.
- The default output could have shown this if nested BrowserTestUtils interval markers were shown with their end time, or with the gap between the end of the child and the end of the parent.

## (reviewer) The coarse list times above cost the review too

- Same command shape, same `t=1m3s` rows; every ordering check needed `marker info ... --json` plus a script.
