## No way to get a passing run of the same test on the same config

- Command: `fx-tests test dom/tests/mochitest/general/test_storagePermissionsReject.html --task-ids --limit 0`
- Question: a passing job of test-android-em-14-x86_64/debug-geckoview-mochitest-plain that ran this test, to compare its resource-usage profile with the failing one.
- Expected: something like `--task-ids --status pass` (or `--coverage --task-ids`) listing a few passing task IDs per config.
- Got: only the failing tasks. Workaround: Treeherder `jobs/<id>/similar_jobs/`, but the first passing job picked (IvTv_PmlQSWIGJJdWJMnMA) had a different chunking and did not run the test at all, which cost a profile load. (It would not have helped much anyway: passing tests' TEST-PASS lines are buffered and not in the resource-usage profile.)
