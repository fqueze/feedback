## `test --bugs` did not surface the test's own tracking bug

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-paused-overlay.js --bugs`
- Expected: bug 1868969 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-paused-overlay.js | single tracking bug", RESOLVED FIXED 2026-09-12, whose fix is exactly this failure's mechanism), or a line saying no bug was found / closed bugs are excluded.
- Got: the normal `test` output with no bugs section at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_dbg-paused-overlay&include_fields=id,summary,status,resolution"`. A resolved-fixed tracking bug is what tells a `no longer fails` verdict apart from a rare failure that just has not recurred.
