## Question: which of these requests was written to the server first?

- Command: `profiler-cli thread network --sort start --limit 0 --search upgrade --session <s>`
- Expected: per request, the absolute time the request was sent (requestStart) and the time it got a connection, so the wire order of concurrent requests can be read (here: did `?nested-img` reach the server before `?img`).
- Got: start time of the channel and per-phase *durations* only (`TCP=109.75ms`, `TLS=-18,458ns` — negative TLS durations on every request), so the order on the wire is not readable; sorting by start sorts by AsyncOpen, not by send.
- Workaround: `profiler-cli marker info <all Load handles> --json` piped into a script reading `rawFields` `connectStart`/`requestStart`. Those raw fields are on a different time base from the marker's `start` (`startTime` 1243.39 vs `start` 1235.20), so the script has to subtract the offset per marker. Also, `marker info` with several handles wraps the records as `{"markers": [...]}` while a single handle returns the bare record.
- Could have shown: a `req=` (and `conn=`) absolute column in `thread network`, or a `--sort request-start`.
