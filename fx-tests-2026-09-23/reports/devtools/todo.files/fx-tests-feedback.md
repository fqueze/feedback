# fx-tests feedback (building the devtools/ list, 2026-09-21)

## The failing and skipped tests of a folder, both harnesses, in one ranked list

- Commands: `fx-tests issues --harness mochitest|xpcshell --path devtools/ --type fail --type timeout --type crash --group-by test --limit 0 --json`, `fx-tests skips --harness mochitest|xpcshell --path devtools/ --limit 0 --json`.
- Expected: one list across harnesses, each failing test carrying its skip conditions when it is also skipped.
- Got: four separate lists; merging and joining them took a script over `--json`. `issues` rows have a `skipCount` but not the conditions, and `skips` does not say whether the test also fails.
- Could have shown: `issues --harness all`, and the skip conditions on `issues --group-by test` rows with `skipCount > 0`.

## Which of a test's skip conditions are still on main, and the bug next to each

- Command: `fx-tests skips --path devtools/ --limit 0 --json`.
- Expected: the manifest's current `skip-if` lines with their `# Bug NNN` comments.
- Got: the conditions as seen over the window, without comments. 62 of 554 mochitest rows no longer have any `skip-if` on main (19 removed, 43 turned into `run-if`), and 2 more lost one condition; `disabled = "Bug 962258"` comes back as the condition `Bug 962258`. Workaround: parsed all 135 devtools manifests from `git show origin/main:<manifest>`.
- Could have shown: a `removed since` / `now run-if` flag per condition, and `disabled` as its own field.

## Which skipped tests a try push ran on a config where main skips them

- Commands: none in fx-tests. Workaround: the Treeherder jobs API for push 2888bcab0070, then evaluated each manifest condition with manifestparser's `expression.parse` against the mozinfo of each job the push ran.
- Result: 331 of the 471 skip-only rows are skipped only on configs the push did not run (262 of them only on `linux debug http3`, which `!http3` in the try query excluded; 2 `disabled`; the other 67 mac, tsan, standalone, verify).
- Could have shown: `fx-tests try <rev> --skipped-by <path>` or a column in `skips` naming, per condition, whether a given try push ran a matching job.

## The annotated bugs of a folder

- Command: `fx-tests intermittent --harness mochitest --since 21 --limit 0 --json`.
- Expected: `--path devtools/`, as `issues` and `skips` take.
- Got: no `--path`; filtered the 455 mochitest and 60 xpcshell rows to 25 devtools ones by script.
