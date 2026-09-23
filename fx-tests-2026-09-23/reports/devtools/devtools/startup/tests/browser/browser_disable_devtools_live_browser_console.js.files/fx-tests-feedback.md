## `test --bugs` is silent when no bug names the test

- Command: `fx-tests test devtools/startup/tests/browser/browser_disable_devtools_live_browser_console.js --bugs`
- Expected: a line such as "Bugs: none name this test" (or the list).
- Got: stdout byte-identical to the same command without `--bugs`, nothing on stderr. No way to tell "searched, found none" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>` to confirm there is none.
