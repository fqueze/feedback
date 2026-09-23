## A moved test's failures are invisible under its new path

- Command: `fx-tests test browser/base/content/test/browser-webrtc/browser_devices_get_user_media_muted_on_init.js --history`
- Expected: the 6 failures of 2026-09-15, or at least a note that this test only has runs since 2026-09-16 and had earlier runs under another path (it moved from `browser/base/content/test/webrtc/` around 2026-09-17).
- Got: "Verdict: passing. 1,117 of 1,117", with zero runs before 2026-09-16 and nothing saying why.
- Workaround: I noticed the old path in the bug summary and queried it: `fx-tests test browser/base/content/test/webrtc/browser_devices_get_user_media_muted_on_init.js`. It would help if the tool linked the two paths (same file name and same manifest neighbours, with one path's runs stopping the day the other's start), or printed "no runs before <date>: renamed?".

## Question: which revisions of one bug's occurrences fall inside a regressor's landing window

- Command: `fx-tests intermittent --bug 2012281 --tree all --since 90 --limit 0`
- The Occurrences table gives push time and task id, but no revision. To match occurrences against the autoland landings and backouts I had to run `fx-tests task <id>` on each one, and fetch the Treeherder push API by hand.
- A revision column, or the push's first commit summary, would have answered it directly.
