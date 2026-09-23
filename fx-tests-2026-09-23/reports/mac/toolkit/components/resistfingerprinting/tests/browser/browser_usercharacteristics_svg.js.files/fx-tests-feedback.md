## Question: which jobs ran this test and passed it, on a given push or config?

- Command: `fx-tests test <path> --task-ids` lists only the failing tasks; `fx-tests test --help` has nothing for passing ones.
- Needed: a few passing task IDs of the same test (same push, or same config), to check whether a log line present in every failing job is absent from passing ones.
- Workaround: Treeherder `api/jobs/?push_id=<id>` for all mochitest-browser-chrome jobs of the push, then each task's `public/test_info/manifests.list` to keep those that ran the manifest, then each `live_backing.log` to confirm the test ran and passed. About 350 requests.
- What could show it: a `--passing-task-ids [--limit n]` next to `--task-ids`, or the passing tasks per config in `--coverage`.
