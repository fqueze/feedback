## Question: the task IDs and profile URLs of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: the six failing tasks of `browser_tab_splitview_contextmenu.js` with their per-test profile URLs.
- Got: five tasks and "… 1 more task" / "… 1 more profile" inside the row; I went to `--json` and Python before noticing that `--limit 0` (or `--test <path>`) lists them all.
- Suggestion: when a push has only a handful of tasks per test row (here 6), print them all, or put the `--limit 0` hint on the truncated line itself.

