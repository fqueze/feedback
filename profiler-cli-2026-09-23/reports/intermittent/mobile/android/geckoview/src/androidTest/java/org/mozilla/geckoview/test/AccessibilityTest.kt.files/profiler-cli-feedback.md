## Machine CPU over a range, from a resource-usage profile

- Question: "was the machine busier while this test failed than over the rest of the job?"
- Command: `profiler-cli thread markers --session AccessibilityTest.kt-1 --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `data.cpuPercent` inside the test's range and over the whole job.
- Expected: something like `counter info` for the CPU Use markers (min/mean/max per range, over-time buckets). The resource-usage profile has no counters (`counter list` is empty); the CPU is only in 20,603 `CPU Use` interval markers.
- Got: one row per 100 ms marker, with the percentage only in `marker info`, so it takes a script.

## (review) Same CPU question, hit again by the reviewer

- Command: `profiler-cli thread markers --session review-AccessibilityTest.kt-1 --search "CPU Use" --list --limit 0` inside a `zoom push 144.0,146.5`: 26 rows with durations, no percentages. Needed `--json` plus a script over `flatMarkers[].data.cpuPercent` for min/max/median.

## (review) Which tests failed unexpectedly in this job

- Question: "the tests that failed unexpectedly in this job", from a resource-usage profile.
- Command: `profiler-cli thread markers --session review-AccessibilityTest.kt-1 --search "FAIL" --list --limit 0`; `--search "TEST-UNEXPECTED"` matched nothing.
- Expected: the unexpected failures only, or the expected status next to each `test` marker.
- Got: 170 rows, every `FAIL — <test>` label looking the same whether its `expected` field is `PASS` or absent (only 2 of them were unexpected). Workaround: `--json` and a script comparing the `status` and `expected` fields.
