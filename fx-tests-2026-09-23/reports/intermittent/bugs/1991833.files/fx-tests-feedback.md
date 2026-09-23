## Question: "which tests were in flight when a job's mass failure started, and when did it start?"
- Command: `fx-tests task K3sp_FuDSjqpb-OqitSHzA --profiles`
- Expected: for a job with 591 failures, a hint that they are one event (all "will retry" within ~1 s of each other, 20 ms after TEST-START, harness "not killing -- proc or pid unknown"), with the onset time and the tests running at that moment.
- Got: 591 per-test entries, each TIMEOUT "Timed out and was force-killed". The onset and the in-flight set needed a script over live_backing.log.
- Workaround: parsed TEST-START / "will retry" / TEST-PASS lines from the log with Python.

## Question: "every job annotated with bug N beyond the last 7 days"
- Command: `fx-tests intermittent --bug 1991833`
- Expected: a --since/--days option to reach further back.
- Got: 7 days only (10 jobs). Used `https://treeherder.mozilla.org/api/failuresbybug/?startday=...&bug=1991833` (111 jobs since 2026-08-11), which also exposed 7 annotated jobs where the test I was using as a proxy passed.
