## Question: the failing tasks and profiles of one test on a try push

- `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids` then grepping for the test: no match. The row was past the default limit of 10 per section ("… 61 more (--limit 0 for all)").
- `fx-tests try <rev> --config devtools-chrome ...`: refused, because `--config` cannot be applied to try.
- `fx-tests try <rev> --all-jobs --test <path> --task-ids --profiles`: per-config counts (4 jobs failed), but no task IDs or profile URLs, even though both flags were passed.
- Expected: `--test <path>` together with `--task-ids`/`--profiles` lists that test's failing tasks and per-test profile URLs.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0 --messages --full-messages` (2,851 lines), grep for the path. That listed 5 of the 8 task lines ("… 3 more tasks") and all 4 profile pairs.
