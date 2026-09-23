## Question: the per-test profile URL of one test in one job

- Command: `fx-tests task Ot6pdcwPT8uow8cSqwN1Nw.0 --profiles` (job with 23 failing tests)
- Expected: a way to ask for one test's row (e.g. `--test <path>`), since `fx-tests test <path> --task-ids` hands out task IDs precisely to look up that test.
- Got: the FAILED list truncated at `… 3 more (--limit 0 for all)`, with test_Services.js among the hidden rows.
- Workaround: `--limit 0` and grep for the test name.
