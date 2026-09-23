# fx-tests feedback (test_menubar.xhtml)

## Question: the task IDs behind one test's failures on a try push
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --task-ids --profiles --messages --full-messages --limit 0`
- Expected: with `--limit 0`, every task behind the `test_menubar.xhtml` row.
- Got: five `task ...` lines then `… 1 more task`, even with `--limit 0`. The five also repeat each task twice (one per run), so the list is 3 distinct tasks padded to 6 lines and still cut.
- Workaround: `--json` and read `permaFails[].taskIds`. Could have shown one line per distinct task with its run count (`A4K4Dmp8SNa8LeIelC80wg.0 — 2 runs failed`), and honoured `--limit 0`.

## Question: the profiles of the retry run on a try push
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --task-ids --profiles --limit 0 --json`
- Expected: both per-test profiles of each task, since every task failed the test twice (`outcomes.failedTwice: 3`).
- Got: `profiles[].testProfiles` holds only `profile_test_menubar.xhtml.json`; the retry's `profile_test_menubar-2.xhtml.json` is missing, although `fx-tests task <taskId> --profiles` lists it.
- Workaround: `fx-tests task <taskId> --profiles` per task. Could have listed the `-2` profile next to the first one, labelled as the retry.

## Question: how many runs of the test are unaccounted for, and why
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --limit 0`
- Got: `6 failures in 7 runs, across 4 job runs on 2 configs` / `1 run not read`, while `fx-tests try ... --test <path> --all-jobs` lists only 3 job runs, all failed.
- Could have said which job the unread run belongs to and why it was not read (killed for max duration? profile missing?), so the 4th job can be checked on Treeherder.
