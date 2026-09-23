## A passing job of the same config, to compare with a failing one

Question: "give me a task where this test passed in the same config (ideally the same push), so I can compare its resource-usage profile with the failing one".

- Commands tried: `fx-tests test <path> --task-ids` (failures only), `fx-tests task <id> --profiles` (one job).
- Expected: something like `fx-tests test <path> --task-ids --passed --config <cfg> --limit 3`, or `fx-tests task <failing id> --siblings` listing the same-push jobs of sibling variants.
- Got: no way to list passing tasks.
- Workaround: Taskcluster index `gecko.v2.autoland.revision.<rev>.taskgraph.decision` -> `label-to-taskid.json` of the failing push, then guessing the chunk (`debug-geckoview-xpcshell-3`) that ran the manifest, then `fx-tests task` on it.
