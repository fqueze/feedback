## `--bugs` prints nothing when no bug names the test

- Command: `COLUMNS=250 fx-tests test browser/components/urlbar/tests/browser-newtab/browser_mousedown.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, with nothing on stdout or stderr about bugs.
- Workaround: searched Bugzilla's REST API (`/rest/bug?quicksearch=browser_mousedown.js`) to confirm there is none.
- Question: "is there a bug for this test?" "No bug names this test" would answer it.
