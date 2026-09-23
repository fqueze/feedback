# profiler-cli feedback (browser_chat_request.js)

## Question: "at what exact time did this marker happen", in a long (20 min) resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:INFO,name:PASS" --list --limit 0`
- Expected: marker times with millisecond precision (as for short profiles: `t=34.038s`), to align the
  resource-usage profile with the per-test profile.
- Got: `t=1m49s` for every marker in that minute; `marker info m-283` also prints `Time: 1m49s (instant)`.
- Workaround: `--json` and read `.flatMarkers[].start`.
- Could have shown: `t=1m49.468s` (keep ms precision whatever the profile length), at least in `marker info`.

## Question: "how busy was the machine at time T" in the resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percentage of each `CPU Use` marker in the list row.
- Got: rows with only the name and duration, no value; `counter list` says "No counters in this profile"
  and `profile info` says "No significant activity", so nothing shows machine CPU without JSON.
- Workaround: `--json`, then read `fields[cpuPercent]` from each marker with a script.
- Could have shown: `cpuPercent` (and idle_pct) in the list row description, or expose these markers as
  a counter so `counter info` gives the over-time table.

## Question: "when was this IPC message sent, and when did it reach the IPC thread" (review)

- Command: `profiler-cli marker info m-77 --session <s>` on an `IPCIn PBrowser::Msg_OnLocationChange`
- Expected: send and receive times on the same timeline as `Time:` and every other marker.
- Got: `Time: 41.389s - 41.568s`, then "Other payload fields (no schema)" with `startTime: 41399.896`, `sendStartTime`, `recvEndTime: 41480.604`: raw values ~10.8 ms ahead of the displayed timeline (same offset in 3 profiles). The report's author mixed the two and got two delays 11 ms short.
- Workaround: subtract `startTime` minus the displayed start from each raw field.
- Could have shown: `sendStartTime`/`sendEndTime`/`recvEndTime` converted to the profile timeline (e.g. `Received on IPC thread: 41.470s`), or label the raw block as a different time base.
