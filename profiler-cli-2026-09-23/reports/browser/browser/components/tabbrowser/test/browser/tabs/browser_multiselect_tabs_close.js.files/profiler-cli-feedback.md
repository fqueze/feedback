## `marker info` prints times rounded to the second

- Command: `profiler-cli marker info m-421 m-429 m-379 m-466 m-409 --session <id>`
- Expected: each marker's start and end with ms precision, so the order of events a few ms apart can be read.
- Got: `Time: 1m24s - 1m24s (73.375μs)` for every marker; same in `thread markers --list` (`t=1m24s`). A whole test (and the race) fits within one second, so the text output cannot order anything.
- Workaround: `--json` and read `start`/`end`.
- Question it could not answer: "in which order did these DOM events happen, and which one contains the other".

## `thread markers --list` prints no duration for DOMEvent markers in `--json` `fields`, but a `Latency` that looks like one

- Command: `profiler-cli thread markers --search "eventType:TabSelect,..." --list --json`
- Got: the payload `latency` field is easy to mistake for the marker's duration; the real duration only shows in `marker info` (and there only as the unrounded parenthesised value).
- Workaround: `marker info` for each handle.
- Correction to the item above: the flat-marker JSON does carry `duration`; I printed `latency` by mistake. The only remaining point is that the default text list shows `instant`/durations but no sub-second start time (first item).
