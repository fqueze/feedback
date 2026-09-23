## Question: the failing tasks and profiles of one test on a try push

- Command (from the brief): `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: the test's row, with its task IDs and profile URLs.
- Got: no row for `dom/events/test/browser_alt_keyup_in_content.js` at all: it sits past the default truncation of the perma-fail list (`… 41 more (--limit 0 for all)`), so a grep for the test name found nothing and read as "it did not fail".
- `fx-tests try <rev> --test <path> --all-jobs --task-ids --profiles` answers "did it fail, per config" but ignores `--task-ids` and `--profiles`.
- With `--limit 0`, the row still cuts the list to 5 task IDs (`… 7 more tasks`) and 5 profile pairs (`… 1 more profile`); getting all 6 tasks took a script over `--json` (`.taskIds`).
- Workaround: `--limit 0`, then a Python walk of `--json` for `taskIds`.
- What would have answered it: `--test <path>` honouring `--task-ids`/`--profiles`, listing every task and every per-test profile (first run and `-2` retry) of that test.
