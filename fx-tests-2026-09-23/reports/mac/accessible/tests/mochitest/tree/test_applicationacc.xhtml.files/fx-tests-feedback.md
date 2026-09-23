## Question: the failing tasks and profile URLs of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
  - Expected: the row for `accessible/tests/mochitest/tree/test_applicationacc.xhtml`, a perma-fail on both a11y configs.
  - Got: no row for it. The PERMA-FAILS section (51 rows) is truncated by the default limit and this test was past the cut; the only truncation notice was at the very bottom of the output, under a different section, so a `rg applicationacc` over the output found nothing and read as "did not fail".
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test accessible/tests/mochitest/tree/test_applicationacc.xhtml --harness mochitest --task-ids`
  - Expected: per-config counts plus the task IDs (and, with `--profiles`, the profile URLs) of the failing runs.
  - Got: the per-config table only (`failed 2` on each config); `--task-ids` printed nothing. It is also the slow `--all-jobs` path.
  - `--config a11y` is refused on `try`.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0 --messages --full-messages > file`, then `rg` the file for the test path and read its row.
- What would have answered it: `--test <path>` printing the task IDs and profile URLs of that test's failures (and working without `--all-jobs` for the failure side), or the default output saying per section how many rows were cut.
