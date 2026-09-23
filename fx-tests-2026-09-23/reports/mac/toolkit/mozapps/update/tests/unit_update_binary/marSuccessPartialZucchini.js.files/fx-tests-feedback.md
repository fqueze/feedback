## Question: in the jobs where this test failed, what else in its directory failed

- Question: "in how many of the 44 failing jobs did another `unit_update_binary` test fail as well, and which".
- Command: a shell loop running `fx-tests task <id>` for each of the 44 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping the failing-test lines for the directory.
- What the output could show: a co-failure summary in `fx-tests test <path> --task-ids`, e.g. per task the other failing tests under the same directory, or counts of the tests that fail most often in the same jobs.
