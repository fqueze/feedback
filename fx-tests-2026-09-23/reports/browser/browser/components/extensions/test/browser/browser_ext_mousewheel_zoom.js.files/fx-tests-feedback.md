## Which test failed first in the same browser, across all of a test's failing jobs

- Question: in each of the 38 jobs where browser_ext_mousewheel_zoom.js failed, did browser_ext_commands_execute_page_action.js (the suspected leaker) fail before it?
- Command: a shell loop of `fx-tests task <taskId> --limit 0 --quiet` over the 38 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping each output for the leaker's row. About a minute, 38 calls.
- Expected: something like `fx-tests test <path> --co-failures` listing, per failing job or aggregated, the tests that failed earlier in the same job/manifest (with their first message), so a victim's leaker shows at the top.
- Got: `fx-tests test` lists only the task IDs; `fx-tests task` answers one job at a time.
- Workaround: the loop above (output in jobs-execute-page-action.txt).
