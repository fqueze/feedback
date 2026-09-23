# profiler-cli feedback (test_DownloadStore.js)

## Question: "machine CPU over time, in a range" (resource-usage profile)

- Command: `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --json`, then a
  Python script bucketing `data.cpuPercent` into 5 s / 30 s bins.
- The resource-usage profile has no counters (`counter list`: "No counters in this profile"), so
  `counter info` cannot answer; the CPU is only in the `CPU Use` markers' payload.
- What could show it: `counter info`-style "over time" output for the `CPU Use` marker field, or
  `thread markers --search 'name:CPU Use' --stats-field cpuPercent --bucket 5s`.

## Question: "where does the test's log go quiet, and for how long"

- Command: `thread markers --category Test --list --limit 0 --json` piped to a script printing
  consecutive markers more than 1 s apart.
- Output with --list is 200+ lines of TEST-PASS; the 7 s and 10 s gaps are the whole finding.
- What could show it: a `--gaps <min>` option on `thread markers --list`, printing a
  "... 9.97 s without markers ..." row between two markers.

## `thread network --sort start` prints no start times

- Command: `profiler-cli thread network --sort start --limit 0`
- Expected: each request's start time, since the order is by start.
- Got: durations and phases only. The start times needed
  `thread markers --category Network --search name:Load --list`.
