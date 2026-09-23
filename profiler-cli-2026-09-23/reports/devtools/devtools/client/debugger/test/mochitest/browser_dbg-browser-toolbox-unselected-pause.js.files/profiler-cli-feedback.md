## Question: "at what millisecond did each step of the test log happen?"

- Command: `profiler-cli thread markers --session <s> --thread t-0 --category Test --search <test file> --list --limit 0`
- Expected: marker start times precise enough to order and subtract steps (e.g. `351.510s`).
- Got: times rounded to whole seconds in a 5-minute profile (`t=5m51s`, `t=5m52s`), so 30 markers share one timestamp and "Browser toolbox process closed" vs the failure cannot be told apart.
- Workaround: the same command with `--json` and a Python one-liner printing `start`. The text output could print `t=351.510s` (or ms) in `--list` mode.
