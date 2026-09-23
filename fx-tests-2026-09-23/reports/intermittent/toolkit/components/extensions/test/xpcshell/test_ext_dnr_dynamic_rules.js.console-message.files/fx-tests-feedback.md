## One failure mode split into two issues by a helper's line number

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_dnr_dynamic_rules.js`
- Expected: one issue for `Did not get expected console message: ...` (the assertion lives in `AddonTestUtils.checkMessages`, not in the test).
- Got: issue 3 (159x, `: 1754`) and issue 4 (149x, `: 1756`), identical text except the line number, which moved when `AddonTestUtils.sys.mjs` changed around 2026-09-11. Neither count alone is the mode's frequency, and the ranking puts both under the 297 timeouts although together they are 308.
- Workaround: ran `--task-ids --issue 3` and `--issue 4` separately and summed them.

## Question: which configs does one failure mode hit?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 3` (then `--issue 4`, then `--issue 1/2/5` for the other modes)
- What it shows: per-date task-id lists (after repeating the whole header and the all-issues config table), not a per-config count for that issue.
- Workaround: `sed -n '/^Task IDs/,$p' | awk '$1 ~ /\.0$/ {print $2}' | sed -E 's/-[0-9]+$//' | sort | uniq -c`.
- What would have answered it: a per-config table (fails / runs / rate) restricted to `--issue <n>`, instead of the all-modes table.
