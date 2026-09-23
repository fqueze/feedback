## Question: in what order did the test's log lines and the network markers happen, to the millisecond?

- Command: `profiler-cli thread markers --category Test --search browser_speculative_connect_not_with_client_cert --list --limit 0 --session <s>`
- Expected: timestamps precise enough to order events a few ms apart (mousedown, the server's "Accepted TLS client connection", the SpeculativeConnect markers).
- Got: past one minute every row reads `t=1m43s` / `t=1m44s`; a mousedown, an accept 0.8 ms later and a mouseup 210 ms later all look simultaneous. `marker info` prints the same `Time: 1m43s`.
- Workaround: `--json` and a python one-liner over `flatMarkers[].start`.
- What would have answered it: millisecond precision in `--list` (e.g. `t=103.459s`), at least when the rows are within the same second.

## Question: did the navigation request fail, and with which status?

- Command: `profiler-cli thread network --search localhost --sort start --limit 0 --session <s>`
- Expected: the request's final status.
- Got: `???  cache=Unresolved  duration=14.977ms`; the payload's `requestStatus: NS_ERROR_CONNECTION_REFUSED` only shows in `marker info`.
- Workaround: `marker info m-2088`.
- What would have answered it: print `requestStatus` in the `thread network` row, especially when it is not NS_OK.

## Question: when, on the profile's timeline, did the request connect?

- Command: `profiler-cli marker info m-2088 --session <s>` (a Network marker)
- Expected: `connectStart`, `connectEnd` and the other phases on the same time base as the marker's own time.
- Got: raw payload values (`startTime: 103678.010083`) about 8 ms off the marker's start in `--list --json` (`103670.044`), so they cannot be compared with other markers directly.
- Workaround: subtract the offset between `startTime` and the listed start by hand.
- What would have answered it: phase times converted to the profile's time base, or the offset printed.
