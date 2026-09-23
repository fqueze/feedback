## `fx-tests test <path> --bugs` printed no bug section

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_computeShortestPaths_01.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no line about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: took the bug (1991833) from a sibling report on the same job, checked with `fx-tests intermittent --bug 1991833`.
