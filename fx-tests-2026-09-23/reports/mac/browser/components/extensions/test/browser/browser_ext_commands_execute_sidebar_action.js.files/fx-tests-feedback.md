## Question: which failing jobs ran a revision from before a given landing?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_sidebar_action.js --task-ids --limit 0` (and `--json`)
- Expected: each task row to carry its repo and revision (and ideally push date/push id), so a failure can be dated against a fix's landing. The `day` field is the run date, which is misleading with backfills (the 3 failures on 2026-09-14 were re-runs of 2026-08-15 revisions).
- Got: task id, job name, status, day, message; no revision in text or JSON.
- Workaround: ran `fx-tests task <id>` for each of 49 jobs to read the revision from its second line, then `hg json-pushes?changeset=<rev>` for the push id and date. ~100 requests.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_sidebar_action.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: output identical to the run without `--bugs`, so "no bug" and "flag ignored/failed" look the same.
- Workaround: Bugzilla REST quicksearch for the test file name (returned no bugs).
