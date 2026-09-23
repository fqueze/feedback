# fx-tests feedback

## Question: did this try push run the test on the platforms its removed skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/storage/test/browser_storage_indexeddb_hide_internal_dbs.js --task-ids`
- Expected: next to the per-config table, a line saying which platforms the push scheduled
  (linux, windows) against where central schedules the test (`--coverage` knows: mac 0/3 ran,
  skipped on every config), so "passed everywhere it ran" cannot be mistaken for "passes where it
  was skipped".
- Got: 7 linux/windows configs, 21/21 passed, no mention that the push had no mac job at all, and
  the skip-if removed was mac-only.
- Workaround: a Python script over the Treeherder jobs API (`/api/jobs/?push_id=...`) counting
  platforms.

## Question: the task IDs of the jobs where the test passed

- Command: same as above, with `--task-ids`.
- Expected: the task IDs behind each row of the per-config table.
- Got: no task IDs printed for passing runs.
- Workaround: Treeherder jobs API script filtering on job_type_name.
