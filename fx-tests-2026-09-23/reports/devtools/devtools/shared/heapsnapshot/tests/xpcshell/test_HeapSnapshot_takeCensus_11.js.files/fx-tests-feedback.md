## `fx-tests test <path> --bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_takeCensus_11.js --bugs`
- Expected: a line such as "Bugs: none naming this test", so the answer can be read as "no bug".
- Got: the same output as without `--bugs`, with no bug section. That reads the same as the flag being ignored.
- Workaround: took it as "no bug".
