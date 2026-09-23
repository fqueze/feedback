## Question: which bug tracks this test?

- Command: `fx-tests test browser/components/resistfingerprinting/test/browser/browser_roundedWindow_open_max_inner.js --bugs`
- Expected: bug 1781853 (the test's single tracking bug, 15 sheriff annotations in the last 30 days, all of this test's failure), or at least a line saying "no annotated bug in the last N days on trunk".
- Got: the normal `test` output with no bug section at all; `--json` has `annotatedBugs: []`. Nothing says a lookup happened or what window/tree it covered.
- Workaround: Bugzilla quicksearch on the file name, then `fx-tests intermittent --bug 1781853 --since 30 --tree all` (its annotations are on esr153/beta after the trunk fix, and before the 7-day window on trunk).
- What the output could show: the bugs whose summary names the test, even with 0 annotations in the window, with their annotation count per tree.
