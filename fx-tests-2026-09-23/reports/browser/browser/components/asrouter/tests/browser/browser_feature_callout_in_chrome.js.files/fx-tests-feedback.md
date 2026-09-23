## `--bugs` prints nothing when it finds nothing (or misses existing bugs)

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_feature_callout_in_chrome.js --bugs`
- Expected: a Bugs section listing the bugs whose summary names the test (Bugzilla has six, including the tracking bug 1930141 and bug 1968576, still open), or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`; no Bugs section, no "none found" line, nothing on stderr.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>&include_fields=id,summary,status,resolution"`.
