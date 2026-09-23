## `Issues (first failure per run)` names a TODO, not the failure

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js`
- Expected: the first unexpected failure of each run, e.g. `arrowscrollbox is still scrolled to start - false == true`.
- Got: `104x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... tab-group-label`. That message is emitted by `a11yWarn()` in AccessibilityUtils.js, which is `SimpleTest.todo(false, ...)`: an expected TODO, not a failure. The same goes for the `changed preference:` lines in `fx-tests task --messages` (todo when `comparePrefs` is off). The `taskIds[].message` in `--json` carries the same TODO, so no failure mode split is possible from `fx-tests test`.
- Workaround: `fx-tests task <id> --messages` for each of the 94 FAIL tasks, in a shell loop, to find the real assertion.
- Question the default output could have answered: "which assertion actually failed in these runs, and how many runs per assertion". Filtering out TODO / known-fail results before picking the first message would have given it.
