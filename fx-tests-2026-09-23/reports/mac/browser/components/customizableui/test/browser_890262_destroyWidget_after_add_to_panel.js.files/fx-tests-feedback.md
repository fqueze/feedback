## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/customizableui/test/browser_890262_destroyWidget_after_add_to_panel.js --bugs` (same for browser_876926_customize_mode_wrapping.js)
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the normal `test` output with no bug section at all. I could not tell "searched, none found" from "the flag was ignored".
- Workaround: a Bugzilla REST quicksearch by file name.
