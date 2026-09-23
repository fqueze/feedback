## `fx-tests test <path> --bugs` prints nothing about bugs

- Command: `fx-tests test browser/extensions/newtab/test/xpcshell/test_AboutHomeStartupCacheWorker.js --bugs`
- Expected: a Bugs section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: the same output as without `--bugs`, with no bug section and no "none" line, so "no bug" and "flag ignored / lookup failed" look the same. Bugzilla has bug 1966300 naming the test (a -11 return code, old), which suggests a lookup that found no recent annotations, but the output does not say.
- Workaround: Bugzilla REST quicksearch on the test file name.
