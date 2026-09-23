## `fx-tests try <rev> --test <path> --messages` shows no messages

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_clear_method.js --messages`
- Expected: the per-config table, plus the failure messages for the test.
- Got: only the per-config table. `--messages` was silently ignored, with no warning.
- Workaround: ran `fx-tests try <rev> --profiles --task-ids` and searched it for the test.

## Question: "the Windows task IDs behind this test's failures"

- Command: `fx-tests try <rev> --task-ids`
- Got: the first 5 task rows, which covered only 3 distinct tasks, all on linux-asan. Each task
  is listed once per failing execution, so the retry doubles it. Then `… 37 more tasks`.
- Workaround: a Python script over `--json` `permaFails[].taskIds`, deduplicated and filtered
  to Windows.
- Could have shown: one row per distinct task, grouped by config, so that each platform shows
  up within the default limit.
