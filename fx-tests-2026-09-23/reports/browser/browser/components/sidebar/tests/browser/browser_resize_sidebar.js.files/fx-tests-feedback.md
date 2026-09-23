## `--bugs` prints nothing at all for a test with a single tracking bug

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_resize_sidebar.js --bugs`
- Expected: bug 2063582 ("Intermittent browser/components/sidebar/tests/browser/browser_resize_sidebar.js | single tracking bug", 280 sheriff annotations over the last 30 days per `fx-tests intermittent --bug 2063582 --since 30 --tree all`), or at least a line saying no bug was found and over which window.
- Got: the normal `test` output with no bug section and nothing on stderr. Silence reads as "no bug names this test".
- Workaround: found the bug from the fix commit's message, then `fx-tests intermittent --bug 2063582 --since 30 --tree all`.

## Question: "on which revisions (and push dates) did the failing jobs run?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` in a loop over 45 task IDs to read the revision line, then hg json-pushes for each revision's push date.
- The per-day `--history` and the task list are keyed by job date. Here every failure in the window was a backfill run on 2026-09-02 / 09-14 on revisions pushed 08-14/15 and 08-27, i.e. before the fix landed on 08-28, plus one backed-out push. The default output makes it look like a live intermittent ("recent 0.0%" is the only hint).
- What would have answered it: the revision (and its push date) next to each task ID in `--task-ids`, or a warning when failing jobs ran on revisions much older than the job date.
