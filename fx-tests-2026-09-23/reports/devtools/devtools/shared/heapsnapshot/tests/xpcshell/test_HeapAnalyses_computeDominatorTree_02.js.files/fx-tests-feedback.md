## `fx-tests test <path> --bugs` prints nothing when no bug matches

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_computeDominatorTree_02.js --bugs`
- Expected: a `Bugs` section, or a line such as `No bug names this test`.
- Got: exactly the same output as without `--bugs`, so "no bug" looks the same as "the flag did nothing". I checked Bugzilla's REST search by hand to be sure.
