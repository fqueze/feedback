# profiler-cli feedback (browser_dbg-blackbox-all.js)

## Question: "exactly when did marker X happen relative to marker Y?"
- Command: `profiler-cli thread markers --session <s> --category Test --search <file> --list --limit 0`
- Expected: timestamps precise enough to order events a few ms apart (e.g. "waiting for 2 files" vs the `contextmenu` DOMEvent 4 ms later).
- Got: `t=1m40s` for every row past the one-minute mark; sub-second resolution is lost.
- Workaround: `marker info m-a m-b --json | python3 ...` to print `start`. The list could print `t=100.410s` (or ms) instead of rounding to whole seconds after 1 min.

## `marker info <several handles> --json` has no `handle` field per record
- Command: `profiler-cli marker info m-25 m-11 m-24 --session <s> --json`
- Expected: each record to carry its `handle`, so the output can be matched back to the inputs.
- Got: `handle` missing (None); only order identifies the record.

## `thread markers --list --json`: interval markers have `duration`, no `end`
- Minor: had to compute `start + duration`. An `end` field would save a step.

## `screenshots --at <t>` silently returns a stale frame
- Command: `profiler-cli screenshots --session <s> --at 100.40 -o <dir>`
- Expected: a warning when the newest screenshot is far older than the requested time.
- Got: the 30.005 s frame for t=100.40 s (compositor had stopped producing frames). The row shows `t=30.005s`, but nothing flags that it is 70 s stale; easy to misread as "the screen at 100 s". A "latest frame is 70.4 s before the requested time" note would help, and it is itself a finding (compositing stopped).
