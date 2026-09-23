## `fx-tests test <path> --bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_conditional_breakpoint-03.js --bugs`
- Expected: a Bugs section, even if only "no bug names this test" (and ideally the bug sheriffs starred this test's failures on, here bug 1991833 for the msix TIMEOUTs, which names no test).
- Got: exactly the output without `--bugs`, exit 0, nothing on stderr. Cannot tell "searched, found none" from "flag ignored".
- Workaround: read the bug from a sibling report and Bugzilla REST.
