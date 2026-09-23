## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_03.js --bugs`
- Expected: a `Bugs` section, or a line such as `No bug names this test`.
- Got: the same output as without `--bugs`, so "no bug" looks the same as "the flag was ignored".
- Workaround: took it as no bug, and found the job-wide bug (1991833) from sibling reports.
