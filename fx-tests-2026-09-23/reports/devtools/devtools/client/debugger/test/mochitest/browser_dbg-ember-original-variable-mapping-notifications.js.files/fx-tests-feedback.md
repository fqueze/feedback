## Question: is there a bug for this test's failures?

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-ember-original-variable-mapping-notifications.js --bugs`
- Expected: a Bugs section listing bug 2070932 ("Intermittent browser_dbg-ember-original-variable-mapping-notifications.js | Test timed out", RESOLVED FIXED 2026-09-11), or an explicit "no bug found".
- Got: the same output as without `--bugs`, with no Bugs section at all; `--json` has `"annotatedBugs": []`. The bug names the test in its summary but has no sheriff annotations, so it is not found. Silence reads the same as "the flag did nothing".
- Workaround: found the fix with `git log` on the test file, then the bug through its commit message.
- What would have answered it: print "Bugs: none annotated" when empty, and also search Bugzilla for bugs whose summary names the test file, as the brief says `--bugs` "finds any that names the test".
