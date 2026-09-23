## Which tests were running at one moment (resource-usage profile)

- Question: which `test` markers overlap the instant this test's updater ran (t ≈ 1169.4 s in XITA2qBFT5S0WVwX-nq3-A, ≈ 704.4 s in UlwF6yLgSgSlFTnDr5vDiw).
- Command: `profiler-cli thread markers --session <s> --search unit_update_binary --list --limit 0`
- Expected: start and end of each interval marker precise enough to tell overlap at sub-second level, or a way to ask "markers overlapping t".
- Got: starts rounded to the second (`t=19m27s`) and a duration, no end. With ~35 tests starting within 5 s, second-rounded starts cannot tell who overlapped whom.
- Workaround: `--search "name:test" --list --limit 0 --json` and a Python script computing start/start+duration against the instant. A `--overlapping <t>` (or `zoom push t,t` keeping markers that span it) would have answered it directly.

## Was the machine saturated during this test

- Question: CPU use of the whole machine per second over the test's 34 s.
- Command: `zoom push 1166,1201` then `thread markers --search "name:CPU Use" --list --limit 0`
- Expected: a per-second (or per-bucket) summary of the `cpuPercent` field, the way `counter info` prints "over time" buckets.
- Got: 350 individual 100 ms markers whose text rows do not show the percentage.
- Workaround: `--json` and averaging `fields[cpuPercent]` per second in Python.
