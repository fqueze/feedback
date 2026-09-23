## `thread network` hides the status of a failed load

- Command: `profiler-cli thread network --sort start --limit 0 --session <s>`
- Expected: the failed WebSocket handshake load shown with its `requestStatus`, `NS_ERROR_NET_RESET`, which was the whole failure.
- Got: `???  cache=Unresolved  duration=2.440ms`, with no status. The header line said "1 failed", but the row did not say which one or why.
- Workaround: `marker info m-67`, which lists `requestStatus: NS_ERROR_NET_RESET` among the raw fields. One extra call per profile, and only because the URL looked suspicious.
- Could have shown: `NS_ERROR_NET_RESET` (the `requestStatus`) in place of `???` whenever `status` is `STATUS_STOP` without a response status.

## `thread network` prints negative TLS phases for loads through a proxy

- Command: same as above.
- Got: `Phases: DNS=26.083μs  TCP=156.33μs  TLS=-25,958ns ...` on every load in these profiles. They go through the ssltunnel HTTP proxy, where `secureConnectionStart` is later than `connectEnd`.
- Expected: no TLS phase, or a note that the timings are not ordered. A negative duration reads as a tool bug, and it made me doubt the other phases.

## Question: in what order did these connections open, and which one failed before sending anything?

- Command: `profiler-cli thread network --sort start --limit 0 --search https-only --session <s>`
- Expected: per request, absolute `connectStart`, `tcpConnectEnd` and `requestStart`, so that two connects started 0.05 ms apart, and one of them never reaching `requestStart`, can be read off directly.
- Got: phase durations only. The fact that mattered, two connections opened within 0.1 ms, is not visible in durations.
- Workaround: the sibling report's approach: `thread network --json` for the handles, then `marker info <h> --json` once per handle, and a Python table over `rawFields` (`nettable.sh` in this directory). About 40 calls per profile, over 4 profiles.
- Could have shown: a `--timestamps` option printing `connectStart`/`tcpConnectEnd`/`requestStart`/`responseStart`/`endTime` columns.

## Session dir not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error said to set `PROFILER_CLI_SESSION_DIR`. It cost one call (the sibling report logged the same).
