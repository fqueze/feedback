## Question: "the failure messages and profiles of one test on a try push"

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids` (as the brief says for a skipped test)
- Expected: the rows for `devtools/client/netmonitor/test/browser_net_simple-request-data.js`, the test whose skip-if the push removed.
- Got: the test is not in the output; it sits in a `… 1 more (--limit 0 for all)` of the new-failures section, so `rg` over the output found nothing and it first looked like it had not failed. `--test <path>` then gave per-config counts only (4 of 4 jobs failed), without messages or profile URLs; `--config` is refused on `try`.
- Workaround: `--limit 0 --messages --full-messages` over the whole push (2,851 lines) and search it.
- What would have answered it: `--test <path>` also printing that test's messages, task IDs and per-test profile URLs (it already has them), or letting `--test` narrow the default failure view.
