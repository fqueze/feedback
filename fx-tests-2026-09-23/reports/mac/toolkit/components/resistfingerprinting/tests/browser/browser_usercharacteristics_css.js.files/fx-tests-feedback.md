## `test --bugs` with no bug prints nothing about bugs
- Command: `fx-tests test toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_css.js --bugs`
- Expected: a "Bugs: none" line.
- Got: the same output as without `--bugs`; only `--json` (`annotatedBugs: []`) showed there were none.

## Question: a passing job that ran this test's manifest, to compare with the failing ones
- Needed: a passing job on the failing push (or same config) that ran `toolkit/components/resistfingerprinting/tests/browser/browser.toml`, to check its log for a line that all 31 failing jobs have.
- fx-tests has no way to list passing task IDs for a test (`--task-ids` lists failures only; `--coverage` gives configs, not tasks).
- Workaround: Treeherder `/api/jobs/?push_id=`, then each task's definition from the Taskcluster queue, filtering on `MOZHARNESS_TEST_PATHS`. A `--passing-task-ids` (or tasks per config in `--coverage`) would have answered it.

## Question: does one log line appear in every failing job of a test?
- Needed: whether `[ERROR glean_core] Glean should not be initialized multiple times` follows the manifest's first TEST-START in all 31 failing jobs.
- Workaround: downloaded all 31 `live_backing.log` and grepped. `fx-tests errors --message` is per-date and mochitest-wide, not per failing job of a test.
