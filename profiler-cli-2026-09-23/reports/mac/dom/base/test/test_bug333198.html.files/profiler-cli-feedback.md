## `thread markers --list` loses sub-second times late in a long profile

- Command: `profiler-cli thread markers --session <s> --thread t-11 --category Test --search test_bug333198 --list --limit 0`
- Expected: timestamps precise enough to order markers (the profile is 10 min long; the markers are 1 ms apart).
- Got: every row shows `t=10m7s`, so the pass/fail sequence and its distance from other events cannot be read.
- Workaround: `--json` and `start/1000` by script.
