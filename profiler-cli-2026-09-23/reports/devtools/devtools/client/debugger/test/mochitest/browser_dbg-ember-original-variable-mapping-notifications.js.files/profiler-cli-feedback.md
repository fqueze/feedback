## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "name:RDP Front,Worker.postMessage,name:DOMEvent,category:Test"` on a 4m12s profile, zoomed to a 45 s range.
- Expected: start times precise enough to order markers within the same second (ms), and rows in chronological order.
- Got: every row printed `t=3m28s`, and rows were not in start order (e.g. m-1770..m-1786 listed before m-390 though m-390 starts later), so the sequence could not be read. The zoom narrowed to 300 ms still printed `t=3m28s` everywhere.
- Workaround: `--json` and a python script sorting `flatMarkers` by `start` and printing `start/1000` with 3-4 decimals.
- What would have answered it: print times with ms precision (e.g. `3m27.887s`) at least when zoomed below a few seconds, and sort `--list` by start time.

## Question: when did this network request actually go out, next to the other markers?

- Command: `profiler-cli marker info m-1 --session <s>` on a Network marker.
- Expected: `requestStart`, `responseEnd`, etc. in the same time base as the marker's `Time:` line.
- Got: raw fields in another base: `startTime: 3241.1898` for a marker shown at `3.234s`, a 7.07 ms offset (7.26 ms in another profile), so comparing `responseEnd` with other markers' times gives wrong orderings at the ms scale.
- Workaround: `--json`, compute `rawFields.startTime - start` and subtract it from every timing field.
- What would have answered it: print the timing fields relative to the profile zero, as the marker start is, ideally as phases (queued, connect, request, response) with their absolute times.

## Minor: `load` selected a WebExtensions thread by default

- Command: `profiler-cli load <per-test profile URL> --session <s>` (standalone job profile, 19 threads).
- Got: the selected thread was `t-11 (GeckoMain, WebExtensions)`, so the first `thread markers --category Test` returned nothing.
- Workaround: `thread select t-0`.
- Expected: the parent process GeckoMain, as with the other profile.

## Minor (review): `--session` rejected before the subcommand

- Command: `profiler-cli --session <s> thread markers ...`
- Expected: `--session` accepted as a global option, like on most CLIs with a daemon/session model.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <s>` after the subcommand.
- Also hit again in the review: `--list` rows all printed `t=3m28s` in a sub-second window, so ordering needed `marker info --json` (see the first entry).
