## `test --bugs` printed nothing about bugs

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_note_preview.js --bugs`
- Expected: the bugs naming the test (bug 2020468 names it, for another failure mode), or a line saying none were found.
- Got: exactly the output without `--bugs`; no bugs section, no "none" line.
- Workaround: Bugzilla REST for the given bug.
