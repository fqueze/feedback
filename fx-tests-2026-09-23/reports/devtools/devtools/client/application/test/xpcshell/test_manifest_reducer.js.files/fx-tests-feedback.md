## The tests behind one error message, all of them

- Command: `fx-tests errors --harness xpcshell --day 2026-09-14 --message "nsNotifyAddrListener" --limit 0`
- Expected: `--limit 0` to list all 16 tests under "17 occurrences in 16 tests".
- Got: the per-test list stops after 5 with "… 11 more tests", and no hint of how to see them; `--limit 0` only applies to the top table.
- Workaround: `--group-by test --task-ids`, which lists every test and its task IDs.

