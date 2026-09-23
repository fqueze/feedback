## `test --bugs` is silent when it finds no bug
- Command: `fx-tests test browser/components/migration/tests/unit/test_BookmarksFileMigrator.js --bugs`
- Expected: a "Bugs" section, or a line saying no annotated bug names this test.
- Got: exactly the output without `--bugs`; nothing says whether the lookup ran or found nothing.
- Workaround: searched Bugzilla REST by hand.

## Question: "is this failure tied to particular machines?"
- Command: `fx-tests test <path> --task-ids --limit 0`, then one `curl .../queue/v1/task/<id>/status` per task to read `workerId`.
- The 92 failures came from 23 workers, and macmini-r8-98 failed on 11 of them; its last 4 debug-xpcshell jobs all had 34-113 hang crashes, while other workers' jobs had 0.
- What the output could show: the worker ID next to each task ID, and a per-worker count (and ideally a per-worker failure rate among all runs).

## Question: "a passing run of this test on the same config, to compare with"
- No command lists the task IDs of passing runs of a test. I crawled Treeherder pushes near the failure (10 mac1015 debug xpcshell jobs), and none of them ran this test (the chunks depend on the push).
- What the output could show: `--task-ids` for passing runs too, or `--coverage --task-ids`.

## Question: "how many tests hit this failure in each job?" (the job-wide vs. test-specific split)
- Command: `fx-tests task <id> --limit 0 | grep -c "hang at shutdown"`, repeated across jobs.
- What the output could show: the header's outcome count split by failure message. A job with 142 identical CRASH messages says "machine problem" on its own.
