## Question: which bug are this test's failing jobs starred on

- Command: `fx-tests test devtools/server/tests/xpcshell/test_webextension_descriptor.js --bugs`
- Expected: bug 1991833, on which sheriffs starred the failing job LKys8j8iTQKTAN9hKv040g (it shows in `fx-tests intermittent --bug 1991833`), or at least a line saying "no bug names this test; failing jobs are starred on …".
- Got: the same output as without `--bugs`, with no bug section and no "none found" line, so it is not clear the flag did anything.
- Workaround: found the bug from sibling reports, then `fx-tests intermittent --bug 1991833` to match task IDs.
