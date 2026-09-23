## `--issue <n>` renumbers when combined with `--config`

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_contextmenu.js --task-ids --limit 0 --issue 2 --config test-windows11-32-25h2/opt-mochitest-browser-chrome`
- Expected: issue 2 as numbered in the unfiltered `fx-tests test <path>` output ("Found an unexpected popup at the end of test run: panel#tab-preview-panel").
- Got: `Task IDs (issue 2: FAIL Failure details not recorded ...)` and no task ids — with `--config`, the Issues list is re-ranked for that config and issue 2 is a different message.
- Workaround: ran once without `--issue` to read the per-config numbering, then `--issue 1`.
- Suggestion: accept a message substring for `--issue`, or say in the header that numbering is per the current filter.
