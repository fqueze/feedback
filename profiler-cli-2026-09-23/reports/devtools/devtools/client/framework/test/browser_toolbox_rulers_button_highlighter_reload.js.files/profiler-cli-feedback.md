## Question: "in what order, and how many ms apart, did these markers happen?"

- Command: `profiler-cli thread markers --search '...' --list --limit 0` inside a 10 ms zoom
- Expected: timestamps precise enough to order events a millisecond apart. Ordering JSActor
  `SendQuery`/`ReceiveQueryReply` markers across processes against a `TestUtils` wait was the
  whole diagnosis.
- Got: every row says `t=4m5s`. The list is in chronological order, but it gives neither gaps
  nor any way to compare rows across threads (`profile markers --search`). I had to script over
  `--json` (`start`, `duration`) for every step.
- What would have answered it: once the view is shorter than a few seconds, print `t=` in ms,
  or relative to the zoom start, e.g. `t=244790.90ms` or `+3.46ms`.

## `thread markers --list --json`: `flatMarkers[].description` empty for Test markers in one profile

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0 --json --session <second profile>`
- Expected: the message text, as the text output shows it.
- Got: in the second profile, `description` was empty for `INFO`/`TEST-PASS` rows, though the
  same call on the first profile returned it. Workaround: the text output.
