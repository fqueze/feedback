## `fx-tests test` does not say when a test's only failure is a job-wide collapse

- Question: "is this failure about this test, or did the whole job fall over?"
- Command: `fx-tests test devtools/shared/protocol/tests/xpcshell/test_protocol_simple.js --task-ids`
- Expected: a hint that the one failing job (PyUxuOpdQj2b3T3XyQOKpg) also timed out 1,151 other tests.
- Got: only the task id; the answer took a separate `fx-tests task <id>`, whose header ("1154 failing, 1152 TIMEOUT") is what showed it.
- Could show: per failing task, the number of other tests that failed in the same job, or a "job-wide: N tests failed" flag.
