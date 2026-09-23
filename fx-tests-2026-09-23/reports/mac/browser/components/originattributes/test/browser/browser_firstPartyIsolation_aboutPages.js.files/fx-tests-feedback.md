## Question: "a passing run of this test on this config, near this date"
- Needed: a first-run (not retry) pass of the test on test-macosx1500-aarch64/debug-mochitest-browser-chrome, to compare its resource-usage profile with the failing ones (a leak-at-shutdown failure has no per-test profile).
- Command tried: `fx-tests test <path> --task-ids` (failing tasks only), `fx-tests test --help` (no option for passing tasks).
- Workaround: Treeherder `/api/jobs/?push_id=` for central pushes, then each task's `payload.env.MOZHARNESS_TEST_PATHS` to find the chunk carrying the manifest, then `fx-tests task <id>` to confirm it passed. About 10 HTTP calls per push.
- Could have shown: `fx-tests test <path> --task-ids --passing --config <c> --day <d>` listing a few passing task IDs with their resource-usage profile URLs.
