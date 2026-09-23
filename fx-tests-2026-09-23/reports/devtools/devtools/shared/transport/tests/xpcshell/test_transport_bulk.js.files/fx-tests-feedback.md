## Question: which bug are this test's failing jobs starred on?

- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_transport_bulk.js --bugs`
- Expected: bug 1991833, since the only failing job (XfKT04wZR42kSNwV6wuRpQ) is starred on it.
- Got: the usual summary, with no bug listed. The bug's summary names no test, so it does not match.
- Workaround: `fx-tests intermittent --bug 1991833 --since 21 --limit 0`, then grep the occurrences for the task id. That only works once you already know the bug number, which here came from a sibling report.
- Could show: for each failing task id in `--task-ids`, the bug sheriffs starred it on.
