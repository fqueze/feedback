## `thread markers --list --json`: instant markers have no `duration` key

- Command: `profiler-cli thread markers --session <s> --category Test --list --limit 0 --json | python3 -c '... m["duration"] ...'`
- Expected: `duration: 0` (or null) on instant markers, as on interval markers.
- Got: KeyError; the key is absent on instant markers.
- Workaround: `m.get("duration")`.

## Question: "the test's log with millisecond timestamps"

- Command: `profiler-cli thread markers --search <test file> --list --limit 0`
- Expected: timestamps precise enough to order events a few ms apart (the failure here hinged on a 1-24 ms gap between `Services.io.offline = true` and the rejection).
- Got: `t=9m`, `t=10m28s` at whole-second resolution in the default output, so I had to go through `--json` and a script to get `start` in ms.
- What would have answered it: a millisecond-resolution time column in `--list` output (or a `--precise` flag).
