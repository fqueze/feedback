## Which job held the one passing run of a test on a try push

- Question: "on which config and job did this test pass, among the runs of this try push?"
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0` (and the same with `--all-jobs --json`).
- Expected: the passing run's job named next to "Of those runs: 3 runs failed, then failed again on rerun; 1 run passed."
- Got: the count only; the task list names failing tasks only, and the passing configs that never failed (debug) are absent from the row.
- Workaround: `fx-tests try <rev> --all-jobs --test <path>`, which gives the per-config table (debug-mochitest-plain-5 and -xorig-3 passed 2 of 2 each; opt-mochitest-plain-xorig-4 passed 1 of 2). Pointing to `--test` from that row would have saved the search.
