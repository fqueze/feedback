## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `COLUMNS=250 fx-tests test devtools/server/tests/xpcshell/test_breakpoint-20.js --bugs`
- Expected: a "Bugs" section, saying "none found" when there is none.
- Got: the same output as without `--bugs`, with no bug section at all; `--json` has `annotatedBugs: []` and no key for the search. I could not tell "no bug" from "the flag was ignored" without a Bugzilla quicksearch by hand.
- Workaround: `curl -s "https://bugzilla.mozilla.org/rest/bug?quicksearch=test_breakpoint-20.js"`.

## Question: "which message came before the TIMEOUT, across all the failing runs of this test?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_breakpoint-20.js` (Issues block).
- Expected: the 606 timeouts split by the assertion that failed first in each run, because in xpcshell a TIMEOUT often comes after a `TEST-UNEXPECTED-FAIL` that hung the test.
- Got: one row, `606x TIMEOUT Test exceeded time limit`. The real message (`... "debuggerStatement" == "breakpoint"`) shows up only in `fx-tests task <id>`, one job at a time.
- Workaround: a shell loop of `fx-tests task <id> --profiles | grep -A3 <test>` over 10 task IDs, which took several minutes. A per-message count in `fx-tests test`'s Issues (or `--messages`) would have answered it directly.
