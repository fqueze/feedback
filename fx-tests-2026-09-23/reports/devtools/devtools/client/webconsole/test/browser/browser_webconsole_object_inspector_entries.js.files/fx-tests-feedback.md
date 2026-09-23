## Question: which tasks ran this test (passing), so I can open one and confirm it ran?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_webconsole_object_inspector_entries.js --task-ids` (also with `--json`)
- Expected: the task IDs behind each "passed" count, so the passing runs can be opened with `fx-tests task` or their resource-usage profile loaded (to confirm the test ran on the config its `skip-if` had disabled, and on which display server).
- Got: only the per-config counts table; `--task-ids` is silently ignored with `--test`, and the JSON has no task IDs either.
- Workaround: look up task IDs elsewhere (treeherder-cli).
