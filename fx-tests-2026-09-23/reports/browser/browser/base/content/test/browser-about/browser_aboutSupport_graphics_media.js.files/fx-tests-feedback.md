## No way to find a passing job that ran the test

- Question: "a passing job, after the failures stopped, that ran this test", to compare its resource-usage profile with a failing one.
- Commands tried: `fx-tests test <path> --task-ids` (lists failing tasks only), then `fx-tests task <taskId> --passed` on later jobs of the same chunk name (standalone-31). Chunk contents move between pushes, so the same chunk name no longer ran the test.
- Workaround: Treeherder `jobs/?push_id=` for one push, then each task's `MOZHARNESS_TEST_PATHS` from the Taskcluster queue, to find the chunk holding the manifest, then `fx-tests task <id> --passed` to confirm. About 40 HTTP requests per push.
- What could have answered it: `fx-tests test <path> --task-ids --passed` (or `--coverage --task-ids`) listing, per config and day, a few task IDs where the test passed.
