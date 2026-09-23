## `fx-tests test` does not see test-verify failures, so a bug's own failure mode can be missing

- Command: `fx-tests test browser/components/customizableui/test/browser_uidensity_auto_compact.js` (also `--coverage --limit 0`)
- Expected: the failure bug 2059750 is about (`[TV] ... test_collapsed_launcher_width_triggers_compact - Compact disengages once the launcher is expanded - false == true`), 22 sheriff-annotated test-verify jobs.
- Got: only the bc `11px vs 7px` failure; no test-verify config in the coverage table, and nothing saying test-verify jobs are excluded. The default `fx-tests intermittent --bug 2059750` (7 days) said "no sheriff annotations", which read as "nothing to see".
- Workaround: `fx-tests intermittent --bug 2059750 --since 60 --tree all --profiles` found the 22 TV jobs and their profiles.
- Suggestion: say in `fx-tests test` output that test-verify jobs are not counted, or add them; and when `intermittent --bug` finds nothing in 7 days, mention the most recent annotation date.

## `fx-tests test` counts a failing linux swr run as a pass

- Command: `fx-tests test browser/components/customizableui/test/browser_uidensity_auto_compact.js --coverage`
- Expected: `test-linux2404-64/opt-mochitest-browser-chrome-swr` to show at least 1 failure on 2026-09-02.
- Got: `603 runs, 603 pass, 0 fail`, but `fx-tests task WlFc-38pSiOs0a6_NYlqbg` (that config, push f76eb931b267, 2026-09-02) shows the test failing on both executions with the same message as the counted failures.
- Workaround: Treeherder's jobs API per push, then `fx-tests task` on the failed jobs.
