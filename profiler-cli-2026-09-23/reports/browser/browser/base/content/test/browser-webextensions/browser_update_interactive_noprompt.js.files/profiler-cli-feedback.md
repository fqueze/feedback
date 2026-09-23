# profiler-cli feedback

## Question: "how long after the test ended did this window die?"

- Command: `profiler-cli marker info m-129 m-202 --session <s>` on a resource-usage profile
  (21 min long).
- Expected: start and end times precise enough to subtract, e.g. `218.362s – 221.342s`.
- Got: `Time: 3m38s - 3m41s (2.980s)` — rounded to whole seconds, so a DOMWindow marker's end
  cannot be compared with a test marker's end.
- Workaround: `marker info ... --json` and a python one-liner reading `start` / `end`.

## Question: "was the machine busy at the end of the test?"

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` inside a zoom.
- Expected: the CPU percent on each row (it is the whole point of the marker).
- Got: only name, time and duration per row; the value needs `marker info` per marker.
- Workaround: `--json` and a script printing `data.cpuPercent` per marker.
- Also: `profile info` and `counter list` report "No significant activity" / "No counters" for
  resource-usage profiles, so the CPU data is only discoverable through the marker names.
