# fx-tests feedback (test_DownloadStore.js)

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadStore.js --bugs`
- Expected: a "Bugs" section, saying "none annotated" when empty.
- Got: the same output as without `--bugs`; no section at all. `--json` does have `"annotatedBugs": []`.
- Workaround: `--json`, then a Bugzilla summary search by hand to be sure.

## Question: "the passing jobs of this config that ran this test, and how long it took in each"

- Wanted: compare the test's duration (and its manifest neighbours') in failing jobs against passing
  jobs of the same config around the same time, to tell "every run is slow" from "some runs stall".
- Commands tried: `fx-tests test <path> --durations` (only min/median/p95/max per config),
  `fx-tests test <path> --task-ids` (failing tasks only), `fx-tests task <id> --passed` (no durations).
- Workaround: Treeherder REST (`/api/project/autoland/jobs/?job_type_name=...&push_id__gte=...`) to
  find task IDs, `fx-tests task <id> --passed` to check which chunk ran the test, then loading each
  resource-usage profile in profiler-cli to read the `test` markers' durations. Five profile loads.
- What could show it: `fx-tests test <path> --config <c> --passing-task-ids`, or per-execution
  durations in `fx-tests task --passed`.
