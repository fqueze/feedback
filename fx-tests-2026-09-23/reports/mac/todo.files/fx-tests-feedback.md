# fx-tests feedback — list agent, Mac 15.30 scope, 2026-09-22

## Which tests failed on one platform (Mac 15.30), and how that compares with the other platforms

- Command: `fx-tests issues --harness <h> --type fail --type timeout --type crash --group-by test --limit 0`, which refuses `--config`.
- Expected: a way to rank the tests failing on one platform, or on job names matching a substring, with their rate there and elsewhere.
- Got: tree-wide totals only (the documented `canAttributeConfigs: false`), so 5,850 tests had to be run one by one.
- Workaround: `fx-tests test <path> --harness <h> --coverage --json --quiet` on each of 6,200 tests (8 in parallel, about 10 minutes), summing `coverage.configs[]` for `test-macosx1500*` against the rest in a script.
- What would have answered it: `issues --config macosx1500` using the per-test bucket data `test` already reads, with a column for the rate on the other configs.

## Which skipped tests are skipped on one platform

- Command: `fx-tests skips --harness <h> --limit 0 --json`.
- Expected: which configs each skip applied to.
- Got: `jobNames` is `[]` on every row, so the platform only comes from the text of the condition in `messages`, and the counts in `messages` cover every platform together.
- Workaround: the per-config `skipCount`/`skipMessages` from `fx-tests test <path> --coverage --json` on every test with a Mac condition.

## Which try jobs were killed at their maximum duration, and whether their failures show up anyway

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --json --limit 0`.
- Expected: the jobs behind "5 of 236 jobs were killed for exceeding their maximum duration" named, in the text and the JSON.
- Got: only a count on stderr, nothing in the JSON.
- Workaround: `treeherder-cli <rev> --json`, then compared the failed jobs per job name with the task IDs `fx-tests try` read. The five were the Mac chrome-1proc and chrome-no-nv jobs, and every test failing in them failed in other jobs that were read.

## `try --harness` does not filter the harness

- Command: `fx-tests try <rev> --harness xpcshell --json` and `--harness mochitest --json`.
- Expected: xpcshell failures only for `--harness xpcshell`.
- Got: the same 133 rows, mochitest and xpcshell mixed, both times. `--harness` seems to change only the central history it compares with. Not a problem once known, but the help does not say so.
- Workaround: took the harness from `jobNames`.

## Renamed tests are split in two

- Question: how many failures and skipped runs a test had in the window, when its directory was renamed during the window (for example `browser/base/content/test/webrtc/` became `browser-webrtc/` on 2026-09-17, bug 2069131).
- Got: old and new paths are separate tests in `issues`, `skips`, `intermittent` and `test`, each with part of the counts.
- Workaround: `git log -M --diff-filter=R --name-status` on main over the window to map old paths to new ones, then summed the rows (1,110 renames, 77 more `test` calls).
