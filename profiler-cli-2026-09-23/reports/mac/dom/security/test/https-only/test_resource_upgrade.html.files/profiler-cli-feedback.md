## Question: in what order did these requests actually go out on the wire, and which ones waited for a connection?

- Command: `profiler-cli thread network --sort start --limit 0 --search upgrade_insecure --session <s>`
- Expected: per request, the absolute `requestStart` (and `connectStart` / `connectEnd`) timestamps, or a sort by `requestStart`, so the order in which the server received the requests can be read off directly.
- Got: phases as durations (DNS, TCP, TTFB...) and a sort by the marker's start time only. The start time is when the channel was created, not when the request was sent: `?img` started 2nd but was sent last, after a 110 ms TCP connect, and that inversion was the whole diagnosis.
- Workaround: `thread network --json` for the handles, then `marker info <h> --json` once per handle, and a Python script over `rawFields` to tabulate `connectStart`, `tcpConnectEnd`, `requestStart`, `endTime`. That took about 30 calls per profile, over 6 profiles.
- Could have shown: a `--sort request` option, or a `sent at` column (absolute `requestStart`) in the default output.

## `marker info` with several handles and `--json` did not give one parseable document

- Command: `profiler-cli marker info m-725 m-726 ... m-761 --json --session <s> 2>/dev/null | python3 -c 'json.load(sys.stdin)'`
- Expected: a JSON array with one record per handle.
- Got: `json.load` failed with "Expecting value: line 1 column 1". I had discarded stderr, so I did not see why. It may have been one bad handle in the list failing the whole call.
- Workaround: one `marker info --json` call per handle.

## Question: was the machine saturated at the time? (resource-usage profile)

- Command: `profiler-cli load .../profile_resource-usage.json`, then `profile info` and `counter list`.
- Expected: the machine's CPU use over time (the brief describes the resource-usage profile as carrying it).
- Got: `profile info` printed "CPU activity over time: No significant activity", the only thread showed 0.000ms, and `counter list` printed "No counters in this profile." The test and TEST-* markers were readable.
- Workaround: none. The CPU question went unanswered (it was not needed for this diagnosis).

## Session dir not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message told me what to do: set `PROFILER_CLI_SESSION_DIR`. It cost one call.

## (review) Question: did this canceled load's request ever reach the server?

- Command: `profiler-cli thread network --search queryresult --session <s>`, then `marker info <h> --json`
- Expected: some indication that a `canceled` load has no timings because the cancel marker is emitted before timings are copied (`nsHttpChannel::CancelInternal` passes the still-empty `mTransactionTimings`), not because nothing was sent.
- Got: `canceled  duration=5m2s` with no Phases line, which looks the same as a request that never left the queue.
- Workaround: read `nsHttpChannel::CancelInternal` and `OnStopRequest` in Necko, then compare with an answered `?queryresult` in another profile. About 6 calls.
- Could have shown: "timings not recorded for canceled loads" on canceled rows.

## (review) `profile markers` rejects `--list`, but `thread markers` needs it

- Command: `profiler-cli profile markers --category Test --list --limit 0 --session <s>`
- Got: `error: unknown option '--list'`. The same flag is required for a full list on `thread markers`.
- Workaround: drop `--list`. Cost one call.
