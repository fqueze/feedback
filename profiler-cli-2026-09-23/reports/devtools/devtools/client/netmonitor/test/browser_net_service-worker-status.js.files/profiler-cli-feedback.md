## Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --session <s> --thread t-0 --category Test --search browser_net_service-worker-status.js --list --limit 0`
  (and `profiler-cli profile markers --search 'sts=304'`, and `marker info m-23 m-24`)
- Expected: a start time precise enough to order markers a fraction of a millisecond apart (the four
  `service-worker-synthesized-response` notifications were 0.3-0.7 ms apart).
- Got: every marker past the first minute printed as `t=1m55s`, in the list and in `marker info` alike, so
  82 markers over 600 ms all read the same time.
- Workaround: `--json` and a script printing `flatMarkers[].start`. The list could print ms (e.g.
  `t=1m54.6032s`) or seconds with 3+ decimals whatever the magnitude.

## Question: "how long did this Load marker last?"

- Command: `profiler-cli thread markers --thread t-0 --search 'service-workers/' --list --limit 0 --json`
- Expected: an `end` (or duration) for interval markers, as the text list shows (`13.097ms`).
- Got: `end` absent/None in `flatMarkers[]` for the Load and NotifyObservers markers; only the payload's own
  `startTime`/`endTime` fields, on a different time base (offset ~7.8 ms from `start`).
- Workaround: none needed here; noted because the two time bases in one record are easy to mix up.
