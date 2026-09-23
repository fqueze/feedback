# profiler-cli feedback (browser_dbg-event-breakpoints.js)

## Question: did a network request fail, and with which error?

- Command: `profiler-cli thread network --session <s> --search doc-event-breakpoints.html --sort start --limit 0`
- Expected: the failed request with its error (`requestStatus: NS_ERROR_ABORT` in the payload).
- Got: `???  cache=Unresolved  size=1.2KB  duration=46.065ms`. There is no status and no error name, so the failure only showed up through `marker info m-1238`.
- Could show: `requestStatus` whenever it is not NS_OK, e.g. `failed NS_ERROR_ABORT`, plus a "failed: N" count in the Summary.

## Question: which markers have a given raw payload value?

- Command: `profiler-cli thread markers --session <s> --search 'requestStatus:NS_ERROR_ABORT' --list --limit 0`
- Expected: Load 9 (m-1238), whose payload has `requestStatus: NS_ERROR_ABORT`. `marker info` lists that field under "Fields (raw payload, no schema)".
- Got: `No markers match the specified filters.`
- Workaround: `thread network` together with `marker info` on each candidate.

## Question: when did each request start, and over which protocol? (needed a script over --json)

- Command: `profiler-cli thread network --sort start --limit 0`. The text output gives neither a start time nor, for cache hits, a protocol, so I could not see the 30 s gap between the last HTTP/3 exchange and the failed request. `--json` has `startTime` and `httpVersion`.
- Could show: the start time (`t=10m58s`) on each request row.

## Question: which processes were still alive at time T? (needed a script over --json)

- Command: `profiler-cli profile info --all`. Lifetimes are printed as timestamp names (`[ts-0z → ts-WC]`), not times, so I could not tell whether a process ended or started around the failure.
- Could show: seconds next to the ts names, e.g. `[1.0s → 634.1s]`.
