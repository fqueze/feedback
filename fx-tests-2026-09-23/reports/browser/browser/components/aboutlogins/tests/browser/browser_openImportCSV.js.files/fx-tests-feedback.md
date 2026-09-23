## `--bugs` found no bug although a tracking bug names the test

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_openImportCSV.js --bugs`
- Expected: bug 1775754 ("Intermittent browser/components/aboutlogins/tests/browser/browser_openImportCSV.js | single tracking bug", RESOLVED INCOMPLETE by BugBot on 2026-09-21, 3 sheriff annotations in the last 30 days per `fx-tests intermittent --bug 1775754 --since 30 --tree all`).
- Got: the same output as without `--bugs`, no bug section and no "no bugs found" line either.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_openImportCSV'`.
