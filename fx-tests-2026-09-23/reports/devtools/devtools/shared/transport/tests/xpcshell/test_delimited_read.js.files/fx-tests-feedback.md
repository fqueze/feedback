## Question: what happened to one test in one job?

- Command: `fx-tests task XfKT04wZR42kSNwV6wuRpQ --profiles`.
- Expected: the row for `test_delimited_read.js`, the test I came from `fx-tests test --task-ids` with.
- Got: the first rows of 1376 failures, in path order. My test was not among them.
- Workaround: `--limit 0 --passed --messages --full-messages` into a file, then grep. That is 6000 lines to find one 3-line row.
- What would have answered it: a `--test <path>` filter on `task`.
