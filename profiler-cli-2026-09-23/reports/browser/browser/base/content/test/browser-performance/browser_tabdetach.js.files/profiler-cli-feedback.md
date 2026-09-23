## Which IPC message went to which process, and when

- Command: `profiler-cli thread markers --search "PSessionStore" --list --limit 0 --session <s>` (also `--search ConstructBrowser`)
- Expected: each IPCIn/IPCOut row showing its message type and peer, e.g. `IPCOut PSessionStore::Msg_FlushTabState -> Web Content (2/9)`.
- Got: rows with an empty description (`m-132  IPCOut  t=8.991s  -775,243ns  ✗`), so the list cannot say which message it is or where it
  went; only the `Runnable` rows name the message. The negative durations are also confusing.
- Workaround: `--json` and a script reading `fields[messageType]`, `sendThreadName`/`recvThreadName`, `data.otherPid`; or
  `marker info` on each handle. The question was "was a FlushTabState sent to the tab's process at TabClose, and which process
  got the new window's ConstructBrowser".

## When was each process launched

- Command: `profiler-cli profile info --all --session <s>`
- Expected: process start times in seconds.
- Got: `p-11: Web Content (6/9) [pid 10072] [ts-v → end]` — a timestamp handle, not a time.
- Workaround: `profile info --all --json` and reading `processes[].startTime`. The question was "which content processes were
  launched while the test ran".

## Machine CPU over a time range in a resource-usage profile

- Command: `profiler-cli zoom push 125,140` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on a
  `profile_resource-usage.json` session
- Expected: each CPU Use row showing its percentages.
- Got: rows with only a handle, time and duration (`m-345  CPU Use  t=2m12s  94ms  ✗`); `counter list` reports no counters.
- Workaround: `--json` and a script printing `fields[cpuPercent]`, `system_pct`, `idle_pct`. The question was "was the machine
  saturated when the failing step ran".

## (review) A session disappeared mid-use

- Command: `profiler-cli thread select t-15 --session browser-review-browser_tabdetach.js-1` (after ~25 queries on that session)
- Expected: the thread selected.
- Got: `Error: Unknown session browser-review-browser_tabdetach.js-1: no metadata found`. The daemon log ends at `Received message: command` with no reply; the machine's swap was full (7/7 GB) with ~25 sessions running. No message said the daemon died or why.
- Workaround: reload the profile under a new session name and redo the lost state.

## (review) Two processes sharing a reused pid are listed as one, under the older name

- Command: `profiler-cli profile info --all --session <s>` on D-cWwPMWSG-tRe-511KSrA's per-test profile
- Expected: pid 6188 shown as the `Web Content (9/12)` that received `Msg_FlushTabState` at 25.06 s.
- Got: only `p-35: https://example.com (8/15) [pid 6188] [ts-M → ts-n]`, a process that ended at 15.2 s; its thread list holds t-39 (that process) and t-71, whose `thread info` says `Name: Web Content (9/12)`, alive to the end.
- Workaround: `thread info` on each of the process's threads. The question was "which process was pid 6188 at t=25 s".

## (review) Loading a raw artifact URL selects a content process's main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_tabdetach.js.json --session <s>`
- Expected: the parent process main thread selected, as when loading the profiler link with `thread=0`.
- Got: `Selected thread: t-8 (GeckoMain, WebExtensions)`; the next `thread markers --search browser-shutdown-tabstate-updated` returned nothing, with no hint that the thread was the reason.
- Workaround: `thread select t-0` after every load.
