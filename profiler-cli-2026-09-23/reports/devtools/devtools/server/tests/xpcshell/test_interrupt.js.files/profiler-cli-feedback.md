## Marker times past 60 s lose their sub-second precision in `--list`

- Command: `profiler-cli thread markers --session test_interrupt.js-3 --search test_interrupt.js --list --limit 0` (ZtblArCLSx6O6KzfEkL1YQ resource-usage profile)
- Expected: start times at the same precision as below 60 s (e.g. `t=65.123s`), to order a test's start against another marker ~1 s earlier.
- Got: `t=1m5s` for the test marker and `t=1m4s` for the launch failure it has to be compared with; below 60 s the same list prints `t=46.561s`.
- Workaround: none taken (the order was clear enough); `marker info` or `--json` per marker would give it, at one extra call per marker.
