## Question: the task IDs of the jobs where this test passed on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/shared/components/test/chrome/test_list_keyboard.html --task-ids --limit 0` (and the same with `--json`)
- Expected: per configuration, the task IDs (with run numbers) of the jobs that ran the test, so one can be opened with `fx-tests task` / its resource-usage profile to confirm and link the PASS.
- Got: only the counts table (jobs / passed / passed on retry / failed); `--task-ids` is silently ignored, and the JSON has no task IDs either.
- Workaround: took task IDs of the same configs from the failure listing of *other* tests in the push (`fx-tests try <rev> --task-ids`), then `fx-tests task <id> --passed` to find this test's PASS row. That only works for configs where some other test happened to fail; for `test-linux2404-64/opt-mochitest-chrome-1proc` the task list was truncated under another test's row and I did not get one.
