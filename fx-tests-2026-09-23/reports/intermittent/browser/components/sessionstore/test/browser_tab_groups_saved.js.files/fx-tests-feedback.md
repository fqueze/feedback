
## fx-tests: which failing tasks have a per-test profile
- Question: "the failing tasks of this test that have a per-test profile I can load".
- Command: `fx-tests test browser/components/sessionstore/test/browser_tab_groups_saved.js --task-ids --limit 0`, then `fx-tests task <id> --profiles` one by one.
- Got: 80 task ids, 72 of them TSan; the first 5 `task --profiles` calls I made (all TSan) each said "No failing test named a per-test profile in this job". Only ASan and debug-standalone tasks had one.
- Expected: `--task-ids` (or `--profiles`) marking which tasks named a per-test profile, or a note that a config (here linux2404-64-tsan) never uploads them.
- Workaround: probe tasks from other configs.

