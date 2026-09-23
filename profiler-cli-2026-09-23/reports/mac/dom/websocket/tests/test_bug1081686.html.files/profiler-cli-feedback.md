## Question: at what exact time (ms) did two markers ~2 s apart happen in a 19-minute profile?

- Command: `profiler-cli thread markers --search 'Websocket server pid,Application pid' --list --limit 0 --session <id>` on a job's `profile_resource-usage.json` (19 minutes long).
- Expected: marker start times precise enough to subtract (ms), since the list is the job's timeline and the question is "how long after the websocket server was launched did the test connect".
- Got: `t=9m28s` for every marker in that second; the server launch, the ssltunnel launch and the browser launch are indistinguishable.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`. The default output could have printed ms (`t=567.670s`) or at least one decimal once several markers share the same rounded second.
