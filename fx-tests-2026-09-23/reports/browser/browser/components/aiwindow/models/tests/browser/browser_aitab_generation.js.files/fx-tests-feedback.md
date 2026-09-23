## Which bug covers this test?
- Command: `fx-tests test browser/components/aiwindow/models/tests/browser/browser_aitab_generation.js --bugs`
- Expected: bug 2073120 "[Flaky Test] browser_aitab_generation.js" (NEW, filed 2026-09-17), or an explicit "no bug found" line.
- Got: the ordinary `test` output with no Bugs section and no "none found" line, so "none" and "did not search" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_aitab_generation.js"`.
