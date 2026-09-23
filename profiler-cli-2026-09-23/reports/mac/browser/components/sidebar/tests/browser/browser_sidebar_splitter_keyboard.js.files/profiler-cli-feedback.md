# profiler-cli feedback

## Question: in what order did the key press, the `command` event, the DeferredTask run and the test's read of the attribute happen?

- Command: `profiler-cli thread markers --category Test --search browser_sidebar_splitter_keyboard.js --list --limit 0 --session <s>` (3m8s profile), also after `zoom push 177,178.9`.
- Expected: timestamps precise enough to order markers within the test, e.g. `t=177.633s`, at least once zoomed into a 1.9 s range.
- Got: every row printed as `t=2m57s` or `t=2m58s`, before and after zooming. All 60 markers of the test fall within two values, so the list cannot say whether the DeferredTask run came before or after the test's `TEST-PASS` that read the value, and the question was about exactly that 100 ms race.
- Workaround: `--json` and a script (`timeline.py` in this directory) that prints `start` in ms and merges several searches into one chronological list.
- What the output could have shown: millisecond precision in `--list` (at least when zoomed in, or when the range is under a minute), or a flag to pick the time format.

## Review (same question, second occurrence): which DeferredTask timer, if any, fired between a key press and the idle run?

- Command: `profiler-cli zoom push 177.5100,177.5250 --session <s>` (a 15 ms range), then `profiler-cli thread markers --search "-name:CompositorScreenshot" --list --limit 0 --session <s>`.
- Expected: row times precise enough to order markers inside a 15 ms zoom.
- Got: every row still printed as `t=2m58s`, so the list could not say whether a `setTimeout callback` came before or after the `command` DOMEvent 8 ms earlier.
- Workaround: `--json` and a script printing `start` in ms (`review/tl.py`).
- What the output could have shown: time precision that follows the zoom's duration, e.g. `177.5165s` when the view is under a second.
