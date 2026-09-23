## Ordering markers within one second (browser_inspector_switch-to-inspector-on-pick.js)

- Question: "in what order, to the millisecond, did these markers happen?" (a batch timer, an RDP packet, a telemetry record, all within ~200 ms).
- Command: `profiler-cli thread markers --search RDP --list --limit 0 --session S` (also `--search "setTimeout callback"`, `--search WebconsoleAddMessages`, `--category Test --search <test>`).
- Expected: a start time precise enough to order markers (e.g. `t=421.3159s`), at least once zoomed into a sub-second range.
- Got: `t=7m1s` for every row, even while zoomed into a 250 ms range, so every row in the list looks simultaneous.
- Workaround: `--json` piped to a python one-liner printing `flatMarkers[].start` and `.duration`, repeated for every search (about 10 times).
