## `--bugs` prints nothing about bugs

- Command: `fx-tests test devtools/client/netmonitor/src/har/test/browser_net_har_save_as_har.js --bugs` (also for `browser_net_har_post_data.js`)
- Expected: a Bugs section listing bugs naming the test (bug 1948160 "Intermittent .../browser_net_har_save_as_har.js | single tracking bug", RESOLVED INCOMPLETE 2026-05-18; bug 1862292 for post_data), or an explicit "no bug names this test".
- Got: output identical to plain `fx-tests test <path>`; no Bugs section, no "none found" line, so "no bug" and "flag ignored" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>&include_fields=id,summary,status,resolution"`.
