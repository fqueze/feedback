## Which of a leaker's failing jobs did not fail my test, and why

- Question: "the earlier test leaked in 48 jobs; my test failed in 33. In the other 15, did my test run, and did the leak's chain of failures stop before it?"
- Commands: `fx-tests task <id> --passed --limit 0` per job (15 calls), grepping for the test's row and for TIMEOUT/CRASH rows.
- Could show: `fx-tests test <path> --task-ids` could accept `--with-failure-of <other test>` or a `task` view listing, per job, where a run of identical failure messages starts and ends in manifest order.

## `test --task-ids` gives the wrong chunk for a standalone task (review)

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_user_events.js --task-ids --limit 0 --issue 2`
- Expected: `cQBvFxbKS5utodXB0IlyFg.0  test-linux2404-64/opt-mochitest-browser-chrome-standalone-9`, which is what Taskcluster's `metadata.name` and `fx-tests task cQBvFxbKS5utodXB0IlyFg` both say.
- Got: `... opt-mochitest-browser-chrome-standalone-1`.
- Workaround: took the chunk from `fx-tests task <id>` or from `curl .../api/queue/v1/task/<id> | jq .metadata.name`.
