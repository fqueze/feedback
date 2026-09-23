## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_frameId0.js --bugs`
- Expected: a "Bugs" section, or a line such as "No bug names this test".
- Got: the same output as without `--bugs`, with no line about bugs at all, so "no bug" is indistinguishable from "flag ignored" or "search failed".
- Workaround: none; I took it as "no bug".
