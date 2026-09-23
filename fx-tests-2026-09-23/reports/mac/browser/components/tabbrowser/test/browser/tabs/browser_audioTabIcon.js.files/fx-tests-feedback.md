## Question: which try tasks did this test fail in, and where are their profiles?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/components/tabbrowser/test/browser/tabs/browser_audioTabIcon.js --task-ids`
- Expected: the per-config ran/pass/fail table, plus the task ID (and per-test profile URL with `--profiles`) of each job run behind the "passed on retry" and "failed" counts.
- Got: the table only; `--task-ids` is silently ignored with `--test`, and the `--json` output has no task IDs either. Two configs showed "passed on retry 1" with no way to reach the job.
- Workaround: grepped the default (non-`--test`) output for task IDs of those two configs, then ran `fx-tests task <id> --passed` on each candidate to find which one held the timeout.
- Also: without `--test`, the test's two timeouts were inside the KNOWN INTERMITTENTS list truncated at 10 rows ("… 70 more"), so a text search of the default output for the test path found nothing. `--test` is the right entry point for a skipped-test investigation; it just needs the task IDs.
