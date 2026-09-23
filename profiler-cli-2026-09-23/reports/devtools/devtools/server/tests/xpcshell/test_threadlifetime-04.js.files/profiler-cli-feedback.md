# profiler-cli feedback (test_threadlifetime-04.js)

## Searching a marker's displayed label matches nothing
- Command: `profiler-cli thread markers --session <s> --search "TIMEOUT —" --list --limit 0`
- Expected: the `test` markers the list shows as `TIMEOUT — <path>`.
- Got: 0 markers. The label is built from the `status` and `test` fields, and the search only covers the name and the payload values.
- Workaround: `--search status:TIMEOUT`, found with `marker info`.

## Question: "how many tests timed out in this job, and where does mine rank among them?"
- Command: `profiler-cli thread markers --session <s> --search status:TIMEOUT --list --limit 0 --json`, then a script to count, sort by start and find the test's rank.
- What the default output could have shown: a count line is printed already, but a first/last start time for the filtered set (or `--sort start` with a row index) would have answered it without JSON.
