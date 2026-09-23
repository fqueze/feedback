## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_pause_exceptions-02.js --bugs`
- Expected: a `Bugs` section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, so "no bug" looks exactly like "flag ignored". The failures here are all bug 1991833 (win-msix PermissionError), which does not name any test, and nothing pointed there.
- Workaround: found the bug from sibling reports, then `fx-tests intermittent --bug 1991833`.
