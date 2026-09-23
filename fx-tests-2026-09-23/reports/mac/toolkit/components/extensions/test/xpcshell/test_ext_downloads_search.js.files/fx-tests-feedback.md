## `test --task-ids --limit 0` prints a second, unlabelled list

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_downloads_search.js --task-ids --limit 0`
- Expected: one list of the failing tasks.
- Got: the 32-job list, then without a header a second shorter list of dates and task IDs (the jobs that failed more than once, it seems).
- Workaround: guessed from `--executions`. A header such as "Jobs with more than one failing run" would do.
