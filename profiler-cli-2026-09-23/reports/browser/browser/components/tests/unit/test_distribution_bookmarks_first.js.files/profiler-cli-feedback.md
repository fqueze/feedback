## Question: "in which order did these markers happen, to the ms?" — `thread markers --list` rounds to the second in long profiles

- Command: `profiler-cli thread markers --session <s> --search tests/unit/test_distribution --list --limit 0` on a 27-minute xpcshell resource-usage profile.
- Expected: start times precise enough to order the six tests that started within 110 ms of each other, and to compare a log line with the per-test profile.
- Got: every row printed `t=16m12s` or `t=16m13s`, so the order and gaps between setup, PASS and FAIL lines were invisible.
- Workaround: `--json` and a Python one-liner to print `start` in ms. The same for `marker info` (`Time: 1.175s`) when comparing markers 4 ms apart. Showing ms precision when rows share the displayed second would answer it.

## Question: "what time in the job's resource-usage profile is this per-test profile marker?"

- Command: `profiler-cli profile meta --session <s> --json` on both profiles, then subtracting `startTime`s by hand.
- Expected: a way to see a per-test profile marker's time on the resource-usage profile's timeline (or the absolute wall-clock time of a marker), since the brief pairs the two.
- Got: only relative times; `profile meta` prints `Started:` rounded to the ms in its text form, so I needed `--json` for both.
- Workaround: compute the offset from the two `startTime` values (it matched the replayed log lines within 2 ms).
