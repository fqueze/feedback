## `fx-tests try --test` ignores `--task-ids`

- Question: which jobs of the try push ran this test and passed it (to cite them in the report, and open one to confirm it ran rather than skipped).
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_toolbox_console_new_process.js --task-ids --limit 0` (and the same with `--json`)
- Expected: the task IDs behind each configuration row, as `--task-ids` gives for failures.
- Got: only the per-config counts table (jobs / passed / passed on retry / failed); no task IDs in text or JSON.
- Workaround: none; the report cites configs and counts without task IDs.
