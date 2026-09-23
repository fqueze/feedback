## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-cssgrid_01.js --bugs`
- Expected: a "Bugs: none found" line (or the bugs).
- Got: the same output as without `--bugs`; no mention of bugs at all, so "no bug" and "flag ignored" look identical.
- Workaround: a Bugzilla REST summary search.
