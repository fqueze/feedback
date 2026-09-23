# fx-tests feedback (test_bookmark_abort_merging.js)

## `fx-tests test <path> --bugs` prints no bugs section at all

- Command: `COLUMNS=250 fx-tests test toolkit/components/places/tests/sync/test_bookmark_abort_merging.js --bugs`
- Expected: a "Bugs" section, either listing bugs naming the test or saying none was found.
- Got: the same output as without `--bugs`, with no section and no "none" line, so "no bug" and "the flag did nothing" look identical.
- Bugzilla has bug 1573738 (RESOLVED FIXED, 2019), whose summary names this test and the exact failure message `Missing expected exception Should abort merge when signaled`, and bug 1551667 (RESOLVED FIXED). A closed bug for the same message is the prior analysis a diagnosis needs, and the regression history.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=test_bookmark_abort_merging&include_fields=id,summary,status,resolution"`.
- What the output could show: bugs naming the test, including resolved ones marked as such, or an explicit "no bug names this test".
