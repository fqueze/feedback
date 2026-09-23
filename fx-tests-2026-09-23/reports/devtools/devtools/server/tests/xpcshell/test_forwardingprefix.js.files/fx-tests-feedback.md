## Is this test in this job's failures
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles | rg forwardingprefix`
- Expected: the test's row (it is one of the 488 failures).
- Got: nothing; the list is truncated at 20 rows (`… 468 more (--limit 0 for all)`), alphabetical, so a devtools test is past the cut.
- Workaround: `--limit 0`. A `--test <path>` filter on `fx-tests task` would answer it directly.
