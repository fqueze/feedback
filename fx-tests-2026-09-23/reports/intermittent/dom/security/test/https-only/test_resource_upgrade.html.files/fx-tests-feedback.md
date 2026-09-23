## Question: which failing tasks belong to which failure mode?

- Command: `fx-tests test dom/security/test/https-only/test_resource_upgrade.html --task-ids --limit 0`
- Expected: task IDs grouped under the issue (failure message) they showed, or an issue column.
- Got: two consecutive date-ordered lists (2026-09-01 … 09-17, then 2026-09-01 … 09-21 again) under one `Task IDs (165 jobs)` header, with nothing saying the first is the `websocket-unexpected-error` FAIL and the second the TIMEOUT.
- Workaround: rerun once per issue with `--issue 3` / `--issue 4`.
