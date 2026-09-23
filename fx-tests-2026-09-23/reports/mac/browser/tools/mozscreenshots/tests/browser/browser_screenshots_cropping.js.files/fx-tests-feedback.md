## Question: the failures of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`, then searching its output for `browser_screenshots_cropping`.
- Expected: a way to ask for one test's rows on the push (a path argument or `--test <path>`).
- Got: the default list stops at 10 perma-fails (`… 41 more (--limit 0 for all)`), so the test was not in it and the search found nothing; a second full run with `--limit 0` (744+ lines, several minutes) was needed, and a third with `--messages --limit 0` to see all its messages.
- Workaround: `--limit 0` into a file, then `rg -A40` for the test path.
