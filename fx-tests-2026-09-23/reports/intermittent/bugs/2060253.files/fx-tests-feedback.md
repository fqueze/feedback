## Which tests are behind this crash signature?
- Command: `fx-tests crashes --harness mochitest --signature MarkerTiming`
- Expected: the test paths behind the row (it says `tests 2`).
- Got: `33  2  0  @ mozilla::MarkerTiming::Interval` — a count of tests, no names.
- Workaround: read `fx-tests task <id>` for each annotated job of the bug to learn the two paths. A `--tests` (or always listing up to N paths per signature) would answer it directly.

## Crash count and task list disagree
- Command: `fx-tests test dom/animation/test/chrome/test_animation_observers_async.html --task-ids --limit 0`
- Expected: 3 tasks, since the summary says `3 crash`.
- Got: 2 tasks (FBsWw41pQhCdvfAyUY-58A.0, RJ8Cx0mQS4eRxHNF7Euv1A.0), and no line saying why the third is missing.
- Workaround: none; reported on the 2.

## The passing runs of a test on one config
- Question: which jobs of `test-macosx1500-aarch64/debug-mochitest-plain-xorig` ran this test and passed, to compare them with the failing ones.
- Got: `fx-tests test --task-ids` lists only the failing tasks; nothing lists passing task IDs.
- Workaround: `treeherder-cli --repo autoland --similar-history <job id> --json`, then the Treeherder jobs API for each job's task_id.
