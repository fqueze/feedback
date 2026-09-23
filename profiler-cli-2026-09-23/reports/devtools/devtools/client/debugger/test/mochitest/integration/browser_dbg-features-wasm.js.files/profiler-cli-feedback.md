## Question: in what order, to the millisecond, did the markers of one network flow happen?

- Command: `profiler-cli thread markers --session <s> --category Network --list --limit 0` (zoomed to 400 ms)
- Expected: a start time precise enough to order `DispatchTransaction`, `nsHttpChannel::OnStartRequest`, `Suspend`, `Resume` and `OnStopRequest` (they were 0.1 to 140 ms apart), plus the flow id so one channel's markers can be picked out.
- Got: every row read `t=8m15s`, and rows are not strictly chronological (a `Suspend` row came before the `Resume`/`OnStartRequest` rows it follows). Ordering needed `--json` and a script sorting on `start`.
- Workaround: `--json | python3 -c 'sort by start, print start ms'`. Could show: ms (or sub-second) timestamps whenever the view is shorter than a few seconds.

## Question: which requests failed, and with what status?

- Command: `profiler-cli thread network --session <s> --sort start --limit 0`
- Expected: the `requestStatus` (here `NS_ERROR_ABORT`) and missing HTTP status/version called out for a failed document load.
- Got: `???  cache=Unresolved  duration=197.10ms`; the status needed `marker info`, and a comparison with the successful loads needed `--json` and a script (httpStatus/httpVersion per request).
- Workaround: `--json` + script. Could show: `requestStatus` when it is not NS_OK, and a filter such as `--failed`.
