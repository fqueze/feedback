## Question: were these failures on revisions before the fix landed?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar.js --task-ids --limit 0`
- Expected: each failing task's repo and revision, or its push date, next to the task ID. With those, you can tell at once whether the 09-14 cluster ran pre-fix code.
- Got: only the task ID and config, grouped by the day the job ran. The 09-14 jobs had run against revisions pushed on 2026-08-14/15, which this view cannot show.
- Workaround: ran `fx-tests task <id>` once per task (23 calls) to read line 2 (repo and revision). Then I tried hg json-rev to get push dates, and hg-edge answered 406.
