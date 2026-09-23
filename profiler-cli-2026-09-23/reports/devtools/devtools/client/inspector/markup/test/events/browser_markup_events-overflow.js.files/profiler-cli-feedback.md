## `screenshots --range` lists frames from before the range

- Command: `profiler-cli screenshots --range 4.1,48.3 -o <dir> --session <s>` (profile of task aRnREb1HQI25yMUMx93mew, `profile_browser_markup_events-overflow.js.json`)
- Expected: only frames captured between 4.1s and 48.3s, or a clear "no screenshot in range" if there are none.
- Got: `2 screenshot(s) in range 4.100s - 48.300s`, listing `m-293 t=2.798s` and `m-347 t=4.087s`. Both are before the range, and the output is the same as `--at 4.117`. It reads as if frames exist in the range when none were captured after 4.087s.
- Workaround: check each listed frame's `t=` against the range by hand.

## `field:value` in `thread markers --search` is a substring match, with no exact form (review)

- Command: `profiler-cli thread markers --search 'eventType:scroll,eventType:scrollend,eventType:click' --list --limit 0 --session <s>` (profile of task aRnREb1HQI25yMUMx93mew)
- Question: "did any `scroll` event fire?"
- Expected: only DOMEvent markers whose `eventType` is exactly `scroll`, `scrollend` or `click`, or a way to ask for an exact match (e.g. `eventType:=scroll`).
- Got: 50 markers, 49 of them `MozScrolledAreaChanged`. The one real answer (the one `click`) was buried among them.
- Workaround: `| grep -v MozScrolledAreaChanged`.
