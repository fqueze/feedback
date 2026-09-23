## Question: "what was this one test's outcome in this job?"

- Command: `fx-tests task FADMjPT5QXm-7tdUJVLRRQ --profiles`
- Expected: a way to see the entry of the test I am diagnosing (`fx-tests test --task-ids` said it TIMEOUT in this job).
- Got: the FAILED list truncated at 20 (`… 1544 more`), so the test was absent from 3 of the 6 jobs' output, and I had to grep the output of each; nothing says it is in the hidden part.
- Workaround: `--limit 0` and grep. A `--test <path>` filter on `task` (or the per-job outcome in `test --task-ids`) would answer it directly.
