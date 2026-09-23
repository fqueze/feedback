## Question: "did test X fail on this try push, and where are its profiles?"

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: a way to scope the push view to one test path (e.g. `--test accessible/tests/mochitest/events/test_valuechange.html`), since the brief's step for a skipped test is exactly this question.
- Got: 744 lines, perma-fails truncated to 10 of 51 (`… 41 more (--limit 0 for all)`); the test was not in the shown part. Needed `--limit 0` (2,778 lines) and a grep.
- Workaround: `--limit 0` to a file, then `rg` for the path.
