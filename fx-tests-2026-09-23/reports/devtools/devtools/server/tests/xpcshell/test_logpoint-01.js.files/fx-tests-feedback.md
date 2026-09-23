## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_logpoint-01.js --bugs`
- Expected: a line saying no bug names this test, e.g. `Bugs: none found`.
- Got: the usual summary with no bugs section at all. That looks the same as the flag being ignored.
- Workaround: took it to mean "no bug", after checking there was no truncation line.
