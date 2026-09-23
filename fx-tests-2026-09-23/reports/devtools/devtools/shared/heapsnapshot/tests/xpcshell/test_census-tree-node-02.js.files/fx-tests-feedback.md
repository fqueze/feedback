## `failures --message` cannot find a message that `test` lists under Issues

- Command: `fx-tests failures --message "Failure details not recorded" --limit 5`
- Expected: the tests failing with "Failure details not recorded (likely Android or platform logging issue)", which `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-02.js` lists as Issue 1.
- Got: "No failure matched. Searched 4,986 tests in xpcshell-issues.json".
- Workaround: none; I wanted to know how common this Android "no log file" failure is tree-wide, and could not.
