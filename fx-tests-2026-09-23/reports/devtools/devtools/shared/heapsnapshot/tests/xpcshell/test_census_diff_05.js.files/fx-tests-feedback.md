## `--bugs` with no bug found says nothing

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_05.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`, with no Bugs section at all. That cannot be told apart from the flag being ignored.
- Workaround: a Bugzilla REST summary search.
