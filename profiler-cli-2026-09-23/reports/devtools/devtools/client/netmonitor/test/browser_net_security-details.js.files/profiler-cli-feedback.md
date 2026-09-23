## The order and gaps of a test's log lines within one second

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_net_security-details.js --list --limit 0`
- Question: how many ms between "got NetworkEvent", the AsnError console messages, and the next network-event progress line.
- Got: every row shows `t=1m23s`: the time column is rounded to the second, so 20 log lines spanning 700 ms were indistinguishable.
- Workaround: `--json` and a python one-liner printing `start`.
- Suggestion: print ms precision in `--list` (e.g. `t=83.215s`), at least when consecutive rows share the same rounded second.
