## Question: in which subtest is each failure of this issue?

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js --task-ids --limit 0 --issue 3`, then `fx-tests task <id> --messages` for each of the 20 jobs.
- Expected: the subtest (the `add_task` name) next to each message, since a test file with ten subtests fails in different ones. The parent had assumed from the one annotated job that all 22 were in `test_turn_on_scheduled_backups_encryption`; 17 of 20 are in `test_turn_on_scheduled_backups_confirm`.
- Got: messages per test, with counts, and no subtest. The issue label is the TEST-KNOWN-FAIL `handleEvent() was unable to perform a11y checks on hidden node`, and 2 of its 22 runs have no a11y failure at all (their only real failure is the 45 s timeout).
- Workaround: loaded all 20 jobs' resource-usage profiles (about 20 s each) and read the `Subtest` field of the TestStatus markers. Cost: about 10 minutes and 20 profile loads.
- Could have shown: the `subtest` field the structured log already carries, in `--messages` and in `--task-ids` rows.
