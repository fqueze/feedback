# fx-tests feedback — browser_toolbox_options_local_mode.js (2026-09-22)

## `--bugs` with no bug found says nothing
- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_options_local_mode.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look the same.

## A profile holding a passing run of my test
- Question: which failing jobs of *later* tests in the same manifest have a per-test profile that contains a passing run of my test (the only way to compare passing and failing runs).
- Commands: `fx-tests test <later test in manifest> --task-ids --limit 0 --config asan`, then `fx-tests task <id> --profiles` per task, then load and grep for `PASS — <my test>`.
- What could show it: `fx-tests test <path> --passing-profiles`, listing per-test profile URLs from the same job and manifest, taken after my test ran.
