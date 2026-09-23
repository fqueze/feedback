# fx-tests feedback (browser_urlBar_zoom.js)

## A TEST-KNOWN-FAIL is reported as the failure mode of every failing run

- Command: `fx-tests test browser/modules/test/browser/browser_urlBar_zoom.js` (also `--json`, `messages`)
- Expected: Issues to name the unexpected failure, `Zoom reset button is currently hidden - Got false, expected true` (and `Button label updated successfully to 120% - Got 120, expected 110`).
- Got: `33x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: urlbar-zoom-button ...` as the only failure message. In the per-test profile that message is a `TEST-KNOWN-FAIL` (an a11y-check todo, status FAIL / expected FAIL). The resource-usage profile's TestStatus marker names it `FAIL` too, but with `color: orange`, which is probably where the confusion comes from.
- Workaround: `fx-tests task <id> --messages` for each of the 33 tasks, then compare against the per-test profile's markers.

## `--bugs` prints nothing, even though an open bug names the test

- Command: `fx-tests test browser/modules/test/browser/browser_urlBar_zoom.js --bugs`
- Expected: bug 1619835 (ASSIGNED, "Intermittent browser/modules/test/browser/browser_urlBar_zoom.js | Zoom reset button is currently hidden - Got false, expected true"), or at least a "no bugs found" line.
- Got: the same output as without `--bugs`, with no bugs section. `annotatedBugs` in `--json` is `[]` and there is no other bugs key.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_urlBar_zoom.js`.

## Question: "what did each failing run fail with?"

- Command: a shell loop over the 33 task ids running `fx-tests task <id> --messages`, grepping each for the test.
- Could have shown: `fx-tests test <path> --task-ids` could print each run's unexpected-failure messages (or group the task ids by message set) so you can confirm that every failing run has the same failure mode without 33 calls.
