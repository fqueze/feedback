## "What happened to this one test in this job?"

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to see one named test's outcome and messages in a job with 488 failures.
- Got: the list is truncated at ~20 rows (`… 468 more (--limit 0 for all)`), sorted by path, so devtools tests are never in the default view; there is no `--test <path>` filter.
- Workaround: `--limit 0 --quiet > file` and grep for the path, once per task (6 tasks).
