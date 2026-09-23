## Marker times in `thread markers --list` are rounded to the second on long profiles

- Command: `profiler-cli thread markers --session <s> --search test_threadlifetime-01 --list --limit 0` on a 22-minute resource-usage profile.
- Expected: start times precise enough to order markers within one second (ms), since several markers of one test (start, output, retry INFO) fall in the same 250 ms.
- Got: every row shows `t=6m23s`; the order and spacing within that second is invisible.
- Workaround: `--json` and read `flatMarkers[].start`. Question it could have answered: "how far apart are this test's start, its only output line and its end".

## Question: "at what time in profile B is profile A's t=X" (per-test profile vs the job's resource-usage profile)

- Commands: `profiler-cli profile meta --session <per-test> --json` and `profiler-cli profile meta --session <resource-usage> --json`, then subtracting `startTime`s by hand to place the per-test profile's crashes (t=8.28 s) on the resource-usage timeline (t=131.68 s).
- Expected: some way to express a time in wall-clock / epoch terms, or `--at-epoch`, or `profile meta` printing the epoch start in ms in the text output.
- Got: text `profile meta` prints `Started: 2026-09-18T01:50:28.871Z` (ms precision, fine) but it is unclear whether `t=0` in marker lists is exactly that instant (the process line says `[ts<0z → end]`), so the alignment is only good to ~0.1 s.
- Workaround: `--json` `startTime` and arithmetic.

## Question: "which tests were in flight at instant T" in a resource-usage profile

- Command: `thread markers --search name:test --list --limit 0 --json` plus a script filtering start <= T <= end.
- Expected: `zoom push T,T+0.001` then `thread markers --search name:test --list` probably answers it (I did not try it first); if so, a one-line hint in the guide for resource-usage profiles would help.

