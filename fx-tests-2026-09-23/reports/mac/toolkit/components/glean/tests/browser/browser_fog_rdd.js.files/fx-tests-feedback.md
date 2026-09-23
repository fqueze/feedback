## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test toolkit/components/glean/tests/browser/browser_fog_rdd.js --bugs`
- Expected: the bugs that name the test, or a line saying none was found.
- Got: the same output as without `--bugs`, with no bug section and no "none" line, so I couldn't tell whether it had searched. Bugzilla has a RESOLVED single-tracking bug for this test (bug 1798676).
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_fog_rdd&summary_type=substring"`.
