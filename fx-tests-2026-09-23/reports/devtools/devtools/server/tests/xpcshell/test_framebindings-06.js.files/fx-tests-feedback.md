## `fx-tests test <path> --bugs` prints nothing about bugs

- Command: `fx-tests test devtools/server/tests/xpcshell/test_framebindings-06.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: output identical to the command without `--bugs`; no line about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: `fx-tests intermittent --bug 1991833`, the bug known from sibling reports for the msix mode.
