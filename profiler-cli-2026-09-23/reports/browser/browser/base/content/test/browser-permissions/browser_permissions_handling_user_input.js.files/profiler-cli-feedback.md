## Which IPC messages were exchanged? (browser_permissions_handling_user_input.js)

- Command: `profiler-cli thread markers --search PCache --list --limit 0 --session browser-bphui-1`
- Expected: rows naming the IPC message type (e.g. `PCacheStorage::Msg_PCacheOpConstructor`), since that is what matched.
- Got: rows that only read `IPCOut ... t=... ✗` / `IPCIn ...` with an empty description; the message type, direction and peer process are only in `marker info` or `--json` (`data.messageType`, `data.niceDirection`).
- Workaround: `--json` piped into a Python filter printing start, messageType and niceDirection.
- Suggestion: show `messageType` and the peer ("sent to https://example.com (41/42)") in the list row for IPC markers.

## Disk/CPU over a range in a resource-usage profile

- Command: `profiler-cli thread markers --search name:IO --list` on a resource-usage profile.
- Expected: a way to see write MB/s and iowait % over time (the `IO` and `CPU Use` markers are 100 ms samples of a time series).
- Got: one row per 100 ms marker with no values in the row; `counter list` says "No counters in this profile".
- Workaround: `--json` and a script bucketing `data.write_bytes` and `iowait_pct` per second.
- Suggestion: expose these resource-usage markers as counters (so `counter info` and its "over time" buckets work), or print their fields in list rows.

## Which marker does a link's `marker=N` point to? (review of browser_permissions_handling_user_input.js)

- Command: none takes a marker index. What I did: `zoom push <t±1ms>`, then `thread markers --list --limit 0 --json` to collect the handles, then `marker info <all handles> --json`, matching `markerIndex`. I did this for about 20 links across 5 profiles.
- Expected: a way to go from index to marker, e.g. `marker info --index 511209`, or a `markerIndex` column/field in `thread markers --list --json` rows.
- Got: `flatMarkers[]` rows carry no `markerIndex`, so every link check costs a zoom plus a batch `marker info`. Several report links pointed at the `Runnable` marker rather than the `IPCOut` one at the same time; I could only tell by resolving the index.

## Thread CPU use rows do not name the thread

- Command: `profiler-cli thread markers --search 'Thread CPU use' --list --session <s>`
- Expected: the thread label in the row, e.g. `QuotaManager IO: 4ms, 19 wake ups`.
- Got: about 55 identical `Thread CPU use` rows at the same timestamp with an empty description. The label only appears in `marker info` (`Thread CPU use - QuotaManager IO`).
- Workaround: `marker info m-A..m-B | rg -i quota`.
