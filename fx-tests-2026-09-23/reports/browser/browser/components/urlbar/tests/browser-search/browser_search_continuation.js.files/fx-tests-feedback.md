## `fx-tests test --bugs` says nothing when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/browser-search/browser_search_continuation.js --bugs`
- Expected: a "Bugs" section, and when it is empty a line such as "No bug names this test", so "none" can be written in a report.
- Got: output identical to `fx-tests test <path>` without `--bugs`; no section, no empty-result line. Could not tell "no bug" from "flag ignored" or "lookup failed".
- Workaround: Bugzilla REST `quicksearch=browser_search_continuation.js`, which returned no bugs.
