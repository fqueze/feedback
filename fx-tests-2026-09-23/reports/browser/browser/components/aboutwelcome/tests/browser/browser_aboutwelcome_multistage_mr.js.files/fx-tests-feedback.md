## Question: "a passing task of this test on config X, to compare with a failing one"

- Needed: a non-standalone debug job that ran `browser_aboutwelcome_multistage_mr.js` and passed, on the same push as a failure, to see whether the same race happens there without the leak.
- Command tried: `fx-tests test <path> --task-ids`, `--coverage`, `--executions` — they list failing tasks and per-config pass counts, but no passing task IDs.
- Workaround: Treeherder `api/jobs/?push_id=` for the push, then fetched `public/test_info/manifests.list` of each of 21 chunks to find the one that ran `browser/components/aboutwelcome/tests/browser/browser.toml`.
- What could have shown it: `fx-tests test <path> --passing-task-ids --config <substr> [--revision <rev>]`, or `--coverage` printing one sample passing task per config.
