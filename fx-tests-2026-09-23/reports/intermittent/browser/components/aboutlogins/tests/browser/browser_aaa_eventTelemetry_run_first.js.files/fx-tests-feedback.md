## Question: "the most recent failing tasks of this test"

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_aaa_eventTelemetry_run_first.js --task-ids --limit 0`
- Expected: a way to get the newest failing tasks first (their artifacts are least likely to have expired), e.g. `--newest` or newest-first ordering.
- Got: 349 rows sorted oldest day first, preceded by the whole per-config summary; the newest tasks are at the very end.
- Workaround: redirect to a file and `awk '/^  2026-09-20/,0'` (a plain `sed -n '/2026-09-21/,$p'` matched the `Data:` header line instead).
