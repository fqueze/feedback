## The failing tasks of one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/shared/test/browser_treeWidget_mouse_interaction.js --task-ids --profiles --full-messages`
- Expected: the per-config table, plus the failing task IDs, the failure message and profile URLs for this test (I asked for them with `--task-ids --profiles`).
- Got: only the per-config ran/failed table; `--task-ids` and `--profiles` are silently ignored with `--test`. `--config a11y-checks` is refused on `try`.
- Workaround: `fx-tests try <rev> --task-ids --profiles --full-messages --limit 0 --messages` (1455 lines) and searching it for the test path.
- What the output could have shown: under `--test`, the failing task IDs per config and the first message, as `fx-tests test --task-ids` does.
