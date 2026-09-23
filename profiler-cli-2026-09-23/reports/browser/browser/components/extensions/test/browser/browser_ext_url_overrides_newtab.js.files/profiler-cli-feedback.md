## Question: when exactly did each marker happen, in a profile longer than 60 s?
- Command: `profiler-cli thread markers --search "Extension:EmitEvent,TabClose,..." --list --limit 0 --session <s>` on a 1m45s profile
- Expected: times like `t=104.027s`, as in shorter profiles.
- Got: every row shows `t=1m44s`, so the order of events 5 ms apart cannot be read. `marker info` also prints `Time: 1m44s - 1m44s (21.080ms)`.
- Workaround: `--json` and print `flatMarkers[].start / 1000` with a Python one-liner. Keeping ms precision (e.g. `1m44.027s`) in the text output would have answered it.

## Question: which document does this DOMEvent belong to?
- Command: `profiler-cli marker info m-584 --json`
- Expected: `innerWindowID` somewhere in the JSON, since the text output shows it under "Other payload fields".
- Got: the `fields` array has only target, latency and eventType, and I could not find innerWindowID in the JSON I looked at.
- Workaround: grep the text output of `marker info` for `innerWindowID`.

