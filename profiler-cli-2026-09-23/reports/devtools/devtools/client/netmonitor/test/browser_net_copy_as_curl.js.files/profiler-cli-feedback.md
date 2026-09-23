## Question: "at what exact time did these markers start/end?" (needed ms precision past 1 min)
- Command: `profiler-cli thread markers --search ... --list` and `profiler-cli marker info m-186`
- Expected: a start time precise enough to compare events ~10 ms apart (e.g. `t=189.180s`).
- Got: `t=3m9s` / `Time: 3m9s - 3m9s (3.459ms)` — second precision once past one minute. Four jobs' responses all ended within 16 ms of each other, which only showed after reading `start` from `marker info --json` (or the Network payload's raw `endTime`).
- Workaround: `marker info <m> --json` and read `.start`.
- Could have shown: millisecond precision in `--list` and `marker info` text, e.g. `t=3m9.180s`.

## `thread network` phase breakdown misleading for HTTPS through the mochitest proxy
- Command: `profiler-cli thread network --search test-server.sjs`
- Expected: TTFB to reflect when the response headers arrived.
- Got: `TTFB=229.80μs  DL=9.780s` for a request whose `nsHttpChannel::OnStartRequest` came 9.78 s after it started. `responseStart` for these proxied HTTPS requests seems to be the proxy's CONNECT reply, so the whole server delay is reported as Download. Also `TLS=-47,200ns` (negative).
- Workaround: looked up the channel's `nsHttpChannel::OnStartRequest` flow marker.
- Could have shown: a warning when phases are negative/inconsistent, or TTFB taken from OnStartRequest when a flow marker exists.

## `profile info` ignores the zoom for per-thread CPU
- Command: `profiler-cli zoom push 179.9,189.3` then `profiler-cli profile info`
- Expected: per-thread CPU within the zoomed range.
- Got: full-profile totals (identical to unzoomed), while the counters under it were zoom-scoped (sample counts changed). Easy to misread.
- Workaround: `counter info c-1` for the process CPU in the range.

## (review) Question: "what was the machine's mean/max CPU use over this range?" (resource-usage profile)
- Command: `profiler-cli zoom push 427.9,437.6` then `profiler-cli thread markers --search "name:CPU Use"`
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean, max).
- Got: only marker counts and durations; had to `marker info <98 handles> --json` and average `cpuPercent` in a script.
- Could have shown: per-field stats for numeric payload fields in the aggregate `thread markers` view.

## (review) `marker info --json`: Network payload is in `rawFields`, `fields` is empty
- Command: `profiler-cli marker info m-19 m-20 --json`, then read `.fields` for `startTime`/`endTime`.
- Expected: the payload in `fields`, as for Test/CPU markers.
- Got: `fields: []` for Network markers ("raw payload, no schema"); the values are only in `rawFields`. Cost one retry.
- Could have shown: the same key for both, or a note in `--json` output.
