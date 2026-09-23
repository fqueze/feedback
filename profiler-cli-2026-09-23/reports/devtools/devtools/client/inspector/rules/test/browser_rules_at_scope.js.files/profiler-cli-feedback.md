## Question: "how long after its launch did the new content process die, and how long did earlier children take to send their first message?"

- Command: `profiler-cli thread markers --search "Process,crash,ipc:content,-name:IPCIn,-name:IPCOut" --list --limit 200 --session browser_rules_at_scope.js-1`
- Expected: millisecond times, since the whole sequence (launch at 68.729 s, `oop-frameloader-crashed` at 69.031 s) is 300 ms long.
- Got: every row `t=1m9s`. Same for the `InitCrashReporter` rows of the earlier launches (`t=1m3s`).
- Workaround: `--json` and a script printing `flatMarkers[].start`.
- Could have shown: times to the millisecond in `--list` (at least under a zoom shorter than a few seconds).

## Question: "what was the machine's CPU when the child died?" (resource-usage profile)

- Command: `profiler-cli counter list --session browser_rules_at_scope.js-2`
- Got: "No counters in this profile"; `profile info` says "No significant activity". The data is in `CPU Use` markers, one per 100 ms, whose list rows carry no value.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script reading `data.cpuPercent`.
- Could have shown: the `CPU Use` markers as a counter, or their `cpuPercent` in the list label.
