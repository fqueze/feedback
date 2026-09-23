## Question: the task IDs and per-test profile URLs behind one test's failures on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/responsive/test/browser/browser_touch_all_events.js --task-ids --profiles --messages --full-messages --limit 0`
- Expected: the per-config table plus, per failing config, the task IDs and profile URLs (and messages) — the flags were accepted.
- Got: only the per-config jobs/failed/passed table; `--task-ids`, `--profiles` and `--messages` were silently ignored. Without `--test`, the test's row is buried among 35 perma-fails of the push.
- Workaround: `fx-tests try <rev> --task-ids --profiles --messages --json --limit 0`, then `jq` on `.permaFails[] | select(.path|test("touch_all_events"))` for `.taskIds` and `.allMessages`, then `fx-tests task <id> --profiles` for the URLs. The `--test` view could print the task IDs (per config, with the message each failed with) when asked.
