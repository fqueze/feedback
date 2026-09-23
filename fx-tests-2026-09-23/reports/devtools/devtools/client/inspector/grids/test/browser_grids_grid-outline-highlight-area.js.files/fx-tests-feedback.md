## `fx-tests test <path> --bugs` found no bug although one names the test

- Question: which bugs name this test?
- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-outline-highlight-area.js --bugs`
- Expected: bug 1501760 ("Intermittent devtools/client/inspector/grids/test/browser_grids_grid-outline-highlight-area.js | Test timed out -", status NEW) listed, or an explicit "no bugs found" line.
- Got: the same output as without `--bugs`, with no bugs section and no "none found" line, so "no bug" and "flag ignored" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=grid-outline-highlight-area'`.
