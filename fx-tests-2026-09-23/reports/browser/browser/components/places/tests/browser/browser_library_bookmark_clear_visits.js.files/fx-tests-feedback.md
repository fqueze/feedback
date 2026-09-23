## Question: is there a bug for this test?

- `fx-tests test browser/components/places/tests/browser/browser_library_bookmark_clear_visits.js --bugs` printed the usual summary and no Bugs section at all; `--json` has `"annotatedBugs": []`.
- Expected: a line saying none were found, and ideally the open tracking bug naming the test (bug 1924571, "Intermittent …/browser_library_bookmark_clear_visits.js | single tracking bug", NEW), whose only annotations are on mozilla-esr140, which `fx-tests intermittent --bug 1924571 --since 21 --tree all` does find.
- Workaround: Bugzilla REST `bug?summary=browser_library_bookmark_clear_visits`.
