## Question: a passing run of this test on the failing config, to compare its log (bug 2036221)

- Command: `fx-tests test <path> --config macosx1500-aarch64/debug --task-ids` lists only failing tasks. No command gives a task where the test passed on that config.
- Expected: a way to get one or two passing task IDs for a test and config, since the brief says to compare with a passing run.
- Got: nothing.
- Workaround: Treeherder `/api/jobs/?push_id=` for recent mozilla-central pushes, then each job's `public/test_info/manifests.list`, to find the chunk that ran the manifest (it moves between chunks from push to push). That took about 8 calls.
- What would have answered it: something like `fx-tests test <path> --passing-task-ids --config <c> --limit 2`.
