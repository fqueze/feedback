## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/customizableui/test/browser_884402_customize_from_overflow.js --bugs` (same for browser_878452_drag_to_panel.js)
- Expected: a "Bugs" section, saying "none found" when empty.
- Got: the normal `test` output with no bug section at all, so "no bug" and "the flag was ignored" look the same.
- Workaround: took the absence as "none".
