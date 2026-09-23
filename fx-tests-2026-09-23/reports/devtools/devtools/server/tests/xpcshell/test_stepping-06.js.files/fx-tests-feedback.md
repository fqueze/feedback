# fx-tests feedback (test_stepping-06.js)

## Question: "what message did this test fail with in each job?"
- Command: `fx-tests task <taskId> --profiles --limit 0` for the six failing msix tasks.
- Expected: the same message for the same failure mode.
- Got: for the same failure (launch failed, 45 s kill timer fired), three jobs show `Test timed out` and three show `... | Timed out and was force-killed by the harness; ...`, although every resource-usage profile holds the force-killed ERROR line for this test.
- Workaround: read the resource-usage profile's markers.
