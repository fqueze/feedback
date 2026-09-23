## Times past 60 s lose millisecond resolution in text output

- Command: `profiler-cli thread markers --session <s> --search "eventType:click,HTMLTooltip.js:546,events_bundle.js.map" --list --limit 0` and `profiler-cli marker stack m-118 --session <s>` on a debug profile whose test ran at ~102 s.
- Expected: `t=102.509s`-style timestamps, as for markers before 60 s (`t=38.049s`), so rows can be ordered and subtracted.
- Got: `t=1m43s` on every row and `Captured at: 1m43s` on every stack: the click, the source-map load, the subscriber and the timer all look simultaneous, when they are 100 ms apart and their order is the whole diagnosis.
- Workaround: `marker info <handles> --json` and reading `start` / `stack.capturedAt`.

## Question: how long did each setTimeout wait between being scheduled and firing?

- Command: `profiler-cli thread markers --search "HTMLTooltip.js:546" --list --limit 0 --json`, then `profiler-cli marker info <88 handles> --json`, then a script computing `start - stack.capturedAt`.
- The `setTimeout callback` marker's stack is captured when `setTimeout` is called, so `capturedAt` is the scheduling time; the only place the text output shows it is `marker stack`'s `Captured at:` line, one marker at a time. A `delay` / `scheduled at` column on `setTimeout callback` rows in `--list` (or a `--group-by` summary of that delay) would have answered "was this timer late, and is it usually?" in one command.

## Question: which SetNeedStyleFlush markers in a range come from a given JS function?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list` then a loop of `profiler-cli marker stack m-N` over each handle, grepping for `EventTooltipHelper.js` / `HTMLTooltip.js!show`.
- `--search` matches names and payload fields but not stack frames. A filter on the marker's stack (e.g. `--stack-search EventTooltipHelper`) would have answered "when did the source-map subscriber run" directly.
