## `test --task-ids --limit 0` prints a second, unlabelled list of tasks

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_downloads_search.js --task-ids --limit 0` (also with `--issue 3`)
- Question: "which jobs failed, and in which of them did the rerun fail too?"
- Expected: one list under `Task IDs (28 jobs)`, with repeat failures marked, or a second list with its own header.
- Got: after the 28 jobs, a second run of date headers and task IDs (SlCyl…, QNHA…, SqBW…) with no header. It turned out to be the jobs whose rerun also failed, which I only worked out by comparing with `--executions` ("7 of those jobs saw the failure more than once") and `task --profiles`.
- Workaround: `fx-tests task <id> --profiles` for each, looking for a `-2` profile.
