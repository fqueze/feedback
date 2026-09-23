## `test --bugs` does not find the bug filed for this test
- Command: `fx-tests test browser/components/aiwindow/models/tests/browser/browser_search_the_web.js --bugs`
- Expected: bug 2073128 "[Flaky Test] browser_search_the_web.js" (NEW, filed 2026-09-17), and 2057240 "Perma TV browser/.../browser_search_the_web.js".
- Got: the normal `test` output, no bug section and no "no bugs found" line either.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_search_the_web.js"`.
