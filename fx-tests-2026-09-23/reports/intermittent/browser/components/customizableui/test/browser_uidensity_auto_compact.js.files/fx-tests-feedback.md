## `fx-tests test` does not show a bug's test-verify failures

- Command: `fx-tests test browser/components/customizableui/test/browser_uidensity_auto_compact.js` (also `--history`, `--task-ids`)
- Expected: the failure mode of the bug filed on this test (bug 2059750, `[TV] ... Compact disengages once the launcher is expanded - false == true`, 22 sheriff annotations in 60 days, all `test-verify` jobs) to appear under Issues, or at least a note that test-verify jobs are not counted.
- Got: only mochitest-browser-chrome data: 8 FAIL + 4 CRASH, all on 2026-09-02, a different message (a since-backed-out regression). Nothing hinted that the bug's own failure mode exists; it looked like "no longer fails".
- Workaround: `fx-tests intermittent --bug 2059750 --since 60 --profiles` found the test-verify occurrences and their task IDs; `fx-tests task <tv task> --profiles` then worked.
- Suggestion: have `fx-tests test` either include test-verify runs as their own config rows, or say "test-verify jobs not included; N annotated TV failures on bug(s) X" when `--bugs` finds any.
