## Question: is there a bug for this test?

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_edit-value-after-name_04.js --bugs`
- Expected: a Bugs section, even if empty ("no bug names this test"), or the closed ones listed as closed.
- Got: output identical to the call without `--bugs`, with no Bugs section at all. Bugzilla has four bugs with the test in their summary (e.g. bug 1877462, "single tracking bug", RESOLVED INCOMPLETE), so I could not tell "none open" from "the flag did nothing".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test file name>`.

