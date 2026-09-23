## `thread markers --list` rounds times to the second on long profiles

- Question: in what order, and how many ms apart, did the test's INFO/PASS/FAIL log markers arrive? (the whole test lasts 678 ms)
- Command: `profiler-cli thread markers --session <s> --search test_trustworthy_loopback --list --limit 0` on a 19-minute resource-usage profile.
- Expected: start times with ms precision (e.g. 13m24.493s), since the markers of interest are within one second.
- Got: `t=13m24s` / `t=13m25s` for every row; ordering within one second is only implied by row order, and the gaps are not visible.
- Workaround: `--json` and a Python one-liner printing `start` relative to the first marker.
- Could have shown: ms precision always, or relative to the zoom start, or a `--relative-to m-N` option.

