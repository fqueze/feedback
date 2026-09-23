## Retry counts disagree between `test` and `task` (test_closing_connections.html)

- Command: `fx-tests test dom/media/test/test_closing_connections.html` says "9 of 438 failing jobs saw the failure more than once".
- Then `fx-tests task V_HnTJ7eSpSOrbq6DboCOQ --profiles` and `fx-tests task eeCdDc7kSMyk4qG3ktDzFw --profiles` both say "TIMEOUT — 2 failing executions of 2", with a `-2` profile each.
- Expected: jobs whose harness retry also failed to be counted among the "more than once" jobs; with 2 of 2 examined jobs double-failing, 9 of 438 looks too low (or the initial run, logged as `TEST-FAIL`/`TEST-TIMEOUT` rather than `TEST-UNEXPECTED-*`, is counted differently from the retry).
- Got: no way to tell from `test` how often the retry passes, which is what separates an order dependency from a fresh-browser failure.
- Workaround: read the retry profile directly.
