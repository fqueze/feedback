## Question: in what order, and how many ms apart, did these markers run? (report browser_net_resend_cors.js)

- Command: `profiler-cli thread markers --session browser_net_resend_cors.js-1 --thread t-0 --search "ObserveActivityWithArgs,nsTransportStatusEvent,Transaction,OnStartRequest" --list --limit 0` inside `zoom push 99.30,99.60`.
- Expected: start times precise enough to order and space markers within a 300ms zoom (e.g. `t=99.3346s`).
- Got: every row reads `t=1m39s`. Past the first minute the list rounds to whole seconds, whatever the zoom width, so it cannot say that a group of events ran 33ms after `DispatchTransaction`, or 2.5ms before the next group.
- Workaround: a jq script over `--json` (`.flatMarkers[] | .start, .duration`), sorted by start. Used for every timing observation in this report.
- What would have answered it: time precision scaled to the zoom width (or at least ms when zoomed below a few seconds), or a `--precise-times` flag.
