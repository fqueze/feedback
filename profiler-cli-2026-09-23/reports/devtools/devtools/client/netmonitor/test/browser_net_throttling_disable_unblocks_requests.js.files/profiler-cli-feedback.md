## Question: in what order did markers a few ms apart happen?

- Command: `profiler-cli thread markers --search "NetworkThrottleManager,clearNetworkThrottling,..." --list --limit 0 --session <s>` and `profiler-cli marker info m-25 m-33 --session <s>`
- Expected: start times precise enough to order markers inside one second (the race here is 2.6 to 9.6 ms wide).
- Got: every row shows `t=3m51s`; `marker info` shows `Time: 3m51s (instant)`. Only raw payload fields such as `startTime` carry ms.
- Workaround: `--json` piped through python to print `start` for each flat marker. The list could print ms (e.g. `t=231.0609s`) when a zoom is under a few seconds, or always.

## Default thread is not the parent main thread

- Command: `profiler-cli load <windows per-test profile URL> --session <s>` then `profiler-cli thread markers --category Test --search throttling_disable --list --limit 0 --session <s>`
- Expected: the parent process GeckoMain selected, as for the two Linux profiles.
- Got: `t-13 (GeckoMain, WebExtensions)` selected, and `0 markers (filtered from 14328)` with no hint that the test log lives on another thread.
- Workaround: `profile info --search "Parent Process"` then `thread select t-0`.

## Question: when did a network request's phases happen, in the same time base as the other markers? (review)

- Command: `profiler-cli marker info m-28 --session <s>` (and `--json`, `rawFields`) on a Network marker, compared with `start` of Text/Log markers from `thread markers --list --json`.
- Expected: `requestStart`, `responseStart`, `startTime`… in the same base as marker `start`, or labeled as a different one.
- Got: the payload fields are raw and sit `Uptime` ms (10.241 ms in one profile) ahead of every marker `start`. The only sign is that the marker's own `start` differs from its `startTime` field. The report being reviewed read the fields as-is, which doubled a 9.8 ms gap to 20 ms.
- Workaround: subtract (`startTime` − `start`) from every payload timestamp. `marker info` could print the payload timestamps rebased, or at least note the offset next to them.
