## `--bugs` prints nothing at all for a test with an open tracking bug

- Command: `fx-tests test browser/extensions/webcompat/tests/browser/browser_smartblockembeds.js --bugs`
- Expected: a bugs section listing bug 1945222 ("Intermittent browser/extensions/webcompat/tests/browser/browser_smartblockembeds.js | single tracking bug", REOPENED), or an explicit "no bugs found".
- Got: the same output as without `--bugs`, nothing on stderr, no bugs section, no "none" line. Indistinguishable from the flag being ignored.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_smartblockembeds.js'`.

## Question: "where was this uncaught rejection created?"

- Command: `fx-tests task Qikk5BwrQDqZgZAkAnPosA --messages`
- Got: `1x uncaught rejection: Invalid tab ID: 27` only.
- The answer (the rejection's own stack, `openProtectionsPanel@.../trackingProtection.js:304` via `recvAPICall`) is only in the live log's lines after TEST-FAIL; the profile's TestStatus marker carries the harness capture stack (`assertNoUncaughtRejections`), not the rejection's. For "uncaught rejection" / "uncaught exception" failures, showing the structured-log `stack` field of the failure would have answered it without downloading a 7 MB log.
