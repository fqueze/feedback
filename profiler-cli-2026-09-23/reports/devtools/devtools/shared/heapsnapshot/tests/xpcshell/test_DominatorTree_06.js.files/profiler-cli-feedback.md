## test_DominatorTree_06.js (2026-09-22)

- **Question: "how busy was the machine over time?"** in a resource-usage profile. The CPU data are
  `CPU Use` markers (1232 of them), not counters, so `counter list` says `No counters in this profile`.
  Answering took `thread markers --search "name:CPU Use" --list --limit 0 --json` plus a script to average
  per 5 s. Expected: `counter list/info` (or `profile info`'s CPU activity, which said "No significant
  activity") to read these markers as a time series.
- **Question: "when did the tests with status X start and end?"**
  (1149 TIMEOUTs all starting at 52-56 s). Answering took `thread markers --search name:test --list
  --json` plus a script to count by status and bucket start/end times. `--group-by field:status` gives
  counts but not when. A histogram of start times per group would have answered it.
