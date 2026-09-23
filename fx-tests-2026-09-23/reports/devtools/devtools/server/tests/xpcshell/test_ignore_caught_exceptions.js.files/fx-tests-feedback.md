## Question: what was this one test's outcome in a given job

- Command: `fx-tests task IXVU7d2uRqSyzPZzQG3Kpw --passed --limit 0 | grep test_ignore_caught_exceptions.js` (repeated over 6 jobs).
- Expected: a way to ask one job about one test, e.g. `fx-tests task <id> --test <path>`, answering "passed / failed / not in this chunk".
- Got: the full 1,400-line listing to grep; an empty grep does not say whether the test passed outside the listing or was not scheduled in that chunk at all.
- Workaround: saved the listing and grepped for the test's directory to see that the chunk did not contain it.
