# fx-tests feedback (building the browser/ list, 2026-09-22)

## The failing tests of a folder, both harnesses, in one ranked list with their skip conditions

- Commands: `fx-tests issues --harness mochitest|xpcshell --path browser/ --type fail --type timeout --type crash --group-by test --limit 0 --json`, `fx-tests skips --harness mochitest|xpcshell --path browser/ --limit 0 --json`.
- Expected: one list across harnesses, each failing test carrying its skip conditions when it is also skipped.
- Got: four lists (923 mochitest, 537 xpcshell failing rows; 787 and 20 skipped rows), joined by script. Same as on the devtools/ list of 2026-09-21.
- Could have shown: `issues --harness all`, and the skip conditions on `issues --group-by test` rows with `skipCount > 0`.

## A test's failures across a rename

- Commands: `fx-tests issues --path browser/ ...`, then `fx-tests test browser/base/content/test/browser-sync/browser_sync.js`.
- Expected: one test, one row; `test` on the new path counting the failures under the old one.
- Got: bug 2069131 moved 131 failing tests from `browser/base/content/test/<dir>/` to `browser-<dir>/` mid-window, and each is two unrelated rows in `issues`. `fx-tests test` on the new `browser_sync.js` path says 2 failures, "intermittent"; its 2416 failures under `browser/base/content/test/sync/browser_sync.js` are invisible from there. Workaround: chained the renames of `git log --diff-filter=R -M origin/main -- browser/` and merged the rows under the main path.
- Could have shown: follow renames (the manifests of each day say where each test was), or at least a "moved to" / "moved from" line in `test`.

## Which failing tests no longer exist on main

- Command: `fx-tests issues --path browser/ ...`.
- Got: 5 rows for tests deleted on main during the window (e.g. `browser_aiwindow_group_tabs_button.js`, 2328 failures, split into four files by bug 2067919), undistinguished from live ones. Found by checking every path against the manifests on `origin/main`.
- Could have shown: a "last seen" date per test, or a flag when the test is absent from the latest day's manifests.

## Which of a test's skip conditions are still on main, and the bug next to each

- Command: `fx-tests skips --path browser/ --limit 0 --json`.
- Got: the window's conditions without their `# Bug NNN` comments; 28 failing rows carry a condition main no longer has (e.g. the `skip-if = ["debug"]` DEFAULT dropped from `urlbar/tests/browser-newtab/browser.toml`, the `asan`/`debug`/`tsan` skip-if turned into `run-if = ["ccov", "opt"]` in `browser-static/browser.toml`). Workaround: parsed the 357 browser/ manifests from `git show origin/main:<manifest>`.
- Could have shown: a `removed since` / `now run-if` flag per condition, and the manifest comment next to each.

## The annotated bugs of a folder

- Command: `fx-tests intermittent --harness mochitest|xpcshell --since 21 --limit 0 --json`.
- Expected: `--path browser/`, as `issues` and `skips` take.
- Got: no `--path`; filtered the 444 mochitest and 53 xpcshell rows by script, including the old paths of renamed tests.
