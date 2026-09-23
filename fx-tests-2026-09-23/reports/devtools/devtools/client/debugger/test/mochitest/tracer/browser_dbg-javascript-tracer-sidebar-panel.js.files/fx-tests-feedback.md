## `--bugs` is silent when no bug names the test

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-panel.js --bugs`
- Expected: a Bugs section, or a line saying no bug names this test.
- Got: the usual summary with no Bugs section and no line about bugs, so "none found" looks the same as "the flag was ignored".
- Workaround: took the absence as "none", and found the fixing bug from `git log` on the helpers instead.
