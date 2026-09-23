## `fx-tests test --bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/urlbar/tests/browser-search/browser_recentsearches.js --bugs`
- Expected: a Bugs section, or "No bug names this test".
- Got: output identical to the run without `--bugs`.
- Workaround: Bugzilla REST `summary=browser_recentsearches`, which found only old bugs for the pre-move path (1860753, 1910002, both resolved).
