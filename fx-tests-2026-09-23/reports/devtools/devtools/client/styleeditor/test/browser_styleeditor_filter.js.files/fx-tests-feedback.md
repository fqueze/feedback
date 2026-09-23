## `fx-tests test <path> --bugs` says nothing when no bug names the test (browser_styleeditor_filter.js, 2026-09-22)

- Command: `fx-tests test devtools/client/styleeditor/test/browser_styleeditor_filter.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`, with no bug section at all, so "none found" looks the same as "flag ignored".
- Workaround: assumed none.
