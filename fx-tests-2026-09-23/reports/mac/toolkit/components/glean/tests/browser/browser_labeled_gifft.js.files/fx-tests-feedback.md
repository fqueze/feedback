## Which other tests failed in the same jobs as this test?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` for each of the 36 jobs, and grep of the FAILED list.
- Expected: a single view of the tests that co-fail with this one, with counts (here: the four `browser_fog_*.js` tests in 36/36 jobs). That identifies a shared cause or an order dependency at a glance.
- Got: only the task IDs; 36 separate `task` calls were needed.
- Workaround: `xargs -P 8` over the task IDs, then grep.

## `--bugs` with no bug prints nothing about bugs

- Command: `fx-tests test toolkit/components/glean/tests/browser/browser_labeled_gifft.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the same output as without `--bugs`, so I could not tell "no bug" from "flag ignored".
