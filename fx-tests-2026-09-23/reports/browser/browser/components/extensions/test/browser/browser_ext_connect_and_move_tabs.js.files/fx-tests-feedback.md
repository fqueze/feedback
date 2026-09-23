## Which earlier test in the same job left the state behind

- Question: "for each failing job of this test, did browser_ext_commands_execute_page_action.js fail before it in the same job?" (a victim-of check across all 41 jobs).
- Command: `fx-tests test <path> --task-ids --limit 0`, then one `fx-tests task <id> --json` per job.
- What would answer it: an option on `fx-tests test` listing, per failing job, the tests that failed earlier in the same job (or a "co-failing tests" count across the failing jobs).
