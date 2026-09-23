## Which subtest timed out, per failing run

- Question: in which `add_task` did each of the 130 timeouts happen? For a browser-chrome test with many subtests, that tells you whether it is one step failing or several.
- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_contextmenu.js --task-ids --limit 0 --issue 3`
- Expected: the timeout issue split by subtest (`test_hidden_menu - Test timed out`, `test_custom_prompt - Test timed out`, ...), or at least the subtest name next to each task ID.
- Got: one issue, `TIMEOUT Test exceeded time limit`, with the subtest name normalized away. `fx-tests task <id>` shows only `Test timed out` too.
- Workaround: downloaded all 103 `live_backing.log` files and grepped `TEST-(UNEXPECTED-)?FAIL | <path> | <subtest> - Test timed out`. That gave test_hidden_menu 95, test_custom_prompt 30, test_open_tab 4, test_open_sidebar 1. It took about 300 MB of downloads to answer one question.
- Also useful from the same logs: a per-run check for a known diagnostic line (here `doc is unexpectedly null (bug 1478596)`, present in all 103 tasks). Showing "lines the harness logged just before the failure" would have answered it without the logs.
