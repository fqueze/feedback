## The IPC message type of each IPC marker in a list

- Command: `profiler-cli thread markers --thread t-0 --search "messageType:PCacheStorage::Msg_PCacheOpConstructor,messageType:PBackground::Msg_PCacheConstructor,..." --list --limit 0 --session <s>`
- Expected: each row to say which message it is (`PCacheStorage::Msg_PCacheOpConstructor`), plus the other pid, since the search matched on exactly that field.
- Got: rows reading only `IPCOut` / `IPCIn` with a duration, so a chronological list of several message types could not be read. Needed `marker info` per handle, then `--json` and a script over `data.messageType`.
- What the output could show: the `messageType` (and `otherPid`) as the label of IPC markers in `--list`.

## Sub-second times in a long profile's marker list

- Command: `profiler-cli thread markers --search "CPU Use,IO,INFO,FAIL" --list --limit 0 --session <resource-usage profile, 21 min long>`
- Expected: times precise enough to line a 100 ms `CPU Use` sample up with a test log line (ms).
- Got: `t=19m49s` / `t=19m50s` for every row in the test's second, so the order of CPU samples and test messages within it was lost. Needed `--json` and a script relative to a known marker.
- What the output could show: ms precision (e.g. `19m49.924s`), at least when zoomed to a few seconds.

## The CPU % of each resource-usage sample in a window

- Command: same as above.
- Expected: the `CPU Use` rows to show their CPU % and iowait, the `IO` rows their bytes written.
- Got: bare `CPU Use  100ms` rows; the values are only in `marker info` / `--json`.
- What the output could show: the payload's main value as the row label (`55.5% (iowait 7.6%)`, `23.5 MB written`).

## The resource-usage samples at a per-test profile's time (review-browser_target_command_service_workers.js)

- Question: what the CPU and iowait were during a window of the per-test profile, such as 23.60–23.90 s, and how often that iowait level occurs in the same browser session.
- Command: `profiler-cli zoom push <resource-profile seconds> --session <resource-usage profile>` followed by `thread markers --search "CPU Use" --list --limit 0 --json`
- Got: the two profiles have different zero times, so I had to compute the offset from the FAIL marker's start in both, then run a script over `data.iowait_pct`.
- What the output could show: a way to zoom the resource-usage profile to another profile's time range, or to the test's own `test` marker plus an offset. Each row could also show its CPU% and iowait (the existing entry above).
