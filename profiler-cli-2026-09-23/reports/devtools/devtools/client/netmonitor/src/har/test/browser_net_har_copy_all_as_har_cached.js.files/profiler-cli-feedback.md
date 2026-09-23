## Question: "in which order did these markers happen, within the same millisecond?"

- Command: `profiler-cli thread markers --search "http-on,DevTools:RDP,Network event progress,..." --list --limit 0 --session <s>`
- Expected: enough time resolution to order a test log line against the IPC task and the RDP packet around it. Here: the will-navigate flush packet at 2639.2, the Front receiving it at 2639.8, `NetMonitor:NetworkEvent` logged at 2641.6, and the reload's `http-on-opening-request` at 2642.2. The order was the whole argument.
- Got: `t=2.642s` for four markers spread over 1 ms. The list sorts them by start time, but the output cannot show whether an instant marker fell inside a runnable or before it.
- Workaround: `profiler-cli marker info m-a m-b ... --json` piped through a Python one-liner to print `start`/`end`. The list could print 0.1 ms resolution, or only when neighbouring rows share a millisecond. Or `marker info` without `--json` could print the start and end at full precision for several handles in one compact table.
