## `fx-tests test <path> --bugs` prints nothing about bugs when none is found

- Command: `fx-tests test browser/components/urlbar/tests/browser/browser_persist_searchMode.js --bugs`
- Expected: a "Bugs" section, or a line such as "No bug names this test".
- Got: output identical to the run without `--bugs`, so "no bug found" is indistinguishable from "flag ignored".
- Workaround: none; assumed no bug.
