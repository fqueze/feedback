## Question: which configs does each failure mode hit?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue <n>`, piped through awk/sort/uniq to count task IDs per config, once for each issue.
- Expected: `fx-tests test` shows a per-config breakdown for each entry under Issues, or `--issue <n>` shows the per-config table filtered to that issue.
- Got: the per-config table covers all failure modes together. This test has timeouts, almost all on debug, and a line-172 assertion failure that hits opt and debug everywhere. They are two different mechanisms, and the combined table hides that.
- Workaround: count the configs of the `--issue <n> --task-ids` list by hand.
