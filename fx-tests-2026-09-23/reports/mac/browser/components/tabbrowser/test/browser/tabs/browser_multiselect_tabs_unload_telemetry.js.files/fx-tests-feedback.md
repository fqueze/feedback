## Issues block splits one failure mode into one row per run when the message embeds numbers

- Question: how many distinct failure modes does this test have?
- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_multiselect_tabs_unload_telemetry.js`
- Expected: one row, `Memory should go down after unload (before: <n>, after <n>) - <n> < 100000`, 189x.
- Got: 189 rows of `1x`, one per distinct pair of byte counts, so the block reads as 189 different failures and the count of the only failure mode is not shown anywhere.
- Workaround: `--task-ids --limit 0 --json`, then a Python script replacing `\d+` with `N` and counting.
- What the output could show: messages grouped after normalizing numbers (as `intermittent` already does with `<n>` in "finished in <n>ms").

## The failing subtest name is missing from `test` output

- Question: which add_task fails?
- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the subtest name, as `fx-tests intermittent --bug 1935293 --since 21` prints it (`test_unload_selected_tab - Memory should go down ...`).
- Got: the assertion message only.
- Workaround: `fx-tests intermittent --bug ... --since 21`, or reading a profile.
