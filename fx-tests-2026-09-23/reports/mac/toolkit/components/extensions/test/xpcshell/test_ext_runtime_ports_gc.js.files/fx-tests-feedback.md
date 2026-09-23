## Question: does any bug name this test?

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_runtime_ports_gc.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs` (verdict, per-config table, Issues), with nothing about bugs. Absence could mean "none found" or "the flag was ignored".
- Workaround: Bugzilla REST `quicksearch=test_ext_runtime_ports_gc` and a summary search, both empty.
