## Question: "which bugs name this test?"

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_console_many_toggles.js --bugs`
- Expected: bugs 1998960 and 1812593 (both "Intermittent devtools/client/webconsole/test/browser/browser_console_many_toggles.js | single tracking bug"; 1812593 is also cited in the manifest's `skip-if`).
- Got: the same output as without `--bugs`, with no bug section and no "none found" line.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_console_many_toggles"`.
