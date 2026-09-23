## `fx-tests test <path> --bugs` says nothing when it finds no bug

- Question: is there a bug for this test's failure.
- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_reload_iframe.js --bugs`
- Expected: a `Bugs` section, even if it reads "none open" — ideally listing the closed ones too (Bugzilla has 2028757 and 1767673, both RESOLVED INCOMPLETE, naming the test).
- Got: the same output as without `--bugs`; no line mentions bugs, so "no bug" and "flag ignored" look identical.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_inspector_reload_iframe`.
