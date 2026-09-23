## Question: how many runs on the platform the failures are on

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_touchevents-5.html --coverage`
- Expected: a per-platform total (runs / fails), since all 7 failures were on Android and the headline rate divides by Windows runs too.
- Got: a per-config table truncated to 10 rows, plus "android 28/28 ran" (configs, not runs).
- Workaround: `--coverage --json` and summing `coverage.configs[].runCount` for Android job names (6,686 runs, 7 fails).

## Question: a passing task of the same config that ran this test

- Wanted a passing job's resource-usage profile to compare against. `fx-tests test --task-ids` lists failing tasks only.
- Workaround: Treeherder `/api/jobs/?push_id=...` for the failing push, then `fx-tests task <id> --json | grep` per chunk to find the one that ran the test. (It turned out a passing test's log is not kept in that profile anyway, which would be worth saying in the guide.)
