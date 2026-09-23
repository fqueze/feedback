## `--bugs` prints no bug section when it finds none

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_geolocation_indicator.js --bugs` (and the same for the new `browser-siteIdentity` path)
- Expected: a "Bugs" section, even if it just says "none open names this test".
- Got: the same output as without `--bugs`; nothing says whether a search ran. Bugzilla has 5 closed bugs naming the test (e.g. 1820146 "single tracking bug", RESOLVED INCOMPLETE).
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_geolocation_indicator`.

## One test's numbers across a rename take two queries

- Question: "how often does this test fail", for a test moved by bug 2069131 (`siteIdentity/` -> `browser-siteIdentity/`).
- Commands: `fx-tests test <new path>` and `fx-tests test <old path>`, then adding the per-config counts by hand.
- Could have shown: the old path's rows merged in (or a note that the file was renamed, with the old path's totals).
