## Question (review): "how long does this test take when it passes in the parallel phase?"

- Command: `fx-tests test toolkit/components/places/tests/unit/test_origins_parsing.js --durations --limit 0`
- Expected: the pass-duration distribution split by execution mode, as `--executions` does for failures, since a timeout in the parallel phase is judged against parallel-phase passes.
- Got: one distribution per config that mixes parallel passes with the harness's sequential retries. On test-macosx1500-aarch64-vms/debug-xpcshell 92 of the 560 passes are retries of about 3 s, which pull the median (21.4 s) below what parallel runs take, and on linux2404-64-artifact/debug every pass is a retry (median 8.0 s).
- Workaround: reasoned about it from the failure counts; no parallel-only number obtained.
