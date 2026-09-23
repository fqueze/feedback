## `thread markers --list` prints whole-second times on a long profile

- Command: `profiler-cli thread markers --category Test --search bug631374 --list --limit 0 --session browser-bug631374-asan-listitem-1` (4m40s profile, no zoom)
- Expected: marker times precise enough to order them against other markers (ms).
- Got: `t=4m37s`, `t=4m39s` for every row; the INFO, the PASS and the two FAILs within 1 s are indistinguishable.
- Workaround: `--json` and a script printing `start/1000` with 4 decimals.
- Question it could not answer: "in which order, and how far apart, did these markers happen?"

