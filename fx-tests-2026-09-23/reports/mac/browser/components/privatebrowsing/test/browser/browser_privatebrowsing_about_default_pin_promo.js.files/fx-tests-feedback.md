## Question: the task IDs of every failing job of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --task-ids --limit 0`
- Expected: `--limit 0` to list all 6 task IDs of `browser_privatebrowsing_about_default_pin_promo.js` (the guide says truncated lists end with `(--limit 0 for all)`).
- Got: 5 task IDs, then `… 1 more task`, with or without `--limit 0`. Same for `… 1 more profile` with `--profiles`.
- Workaround: `--json`, which also does not name the 6th task in the row I read; I did not need it, the 5 listed were enough.
- What would have answered it: `--limit 0` also lifting the per-row task/profile caps, or `--test <path> --task-ids` printing one line per job.

## `--config` refused together with `--test`

- Command: `fx-tests try <rev> --all-jobs --config browser-chrome --test <path>`
- Expected: `--test` already restricts the report to one test, so `--config` narrowing the jobs read (and making `--all-jobs` fast) seemed harmless.
- Got: exit 1, "--config cannot be applied to try: ... filtering the job set would change what each section means".
- Workaround: ran without `--config`; it read all 236 jobs (several minutes).
