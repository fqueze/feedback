## `fx-tests test <path> --bugs` shows no bug section at all

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_highlight-property.js --bugs`
- Expected: a "Bugs" section listing bugs whose summary names the test (Bugzilla has bug 1884491, "Intermittent .../browser_rules_highlight-property.js | single tracking bug", RESOLVED INCOMPLETE, plus three older ones), or an explicit "no bug found".
- Got: output identical to the run without `--bugs`; no line says whether a search happened or found nothing.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.
