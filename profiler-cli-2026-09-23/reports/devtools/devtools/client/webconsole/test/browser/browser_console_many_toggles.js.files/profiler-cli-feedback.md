## `thread markers --list` drops sub-second precision past 60 s

- Question: "in which order did these markers, 0.5 to 20 ms apart, happen?" (a close landing 2 ms after an RDP response).
- Command: `profiler-cli thread markers --session <s> --search 'name:INFO,name:RDP Front,web-console-destroyed' --list --limit 0` on a profile where the test runs at t=69 s.
- Expected: `t=69.728s`, as for times under 60 s (`t=34.201s`).
- Got: every row reads `t=1m9s` or `t=1m10s`, so the sequence cannot be read. Zooming does not change it.
- Workaround: `--json` piped through a script printing `start/1000`. The JSON `flatMarkers` also had no usable `end` field for interval markers, so durations had to come from the text output.
