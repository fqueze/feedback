## Question: were this test's failures collateral of whole-job breakdowns?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_promise_state-01.js --task-ids --limit 0`
- Question: is this test failing on its own, or is every failing job one where hundreds of tests failed at once?
- Got: six task IDs and "6x TIMEOUT Test exceeded time limit"; nothing says that each of those jobs had 476 to 1563 TIMEOUTs. I had to run `fx-tests task <id>` six times to see it.
- Could have shown: per failing task, the job's failing-test count (e.g. `Q6pD… msix  477 failing of 772`), or a verdict line "all 6 failures are in jobs with >400 failing tests".
