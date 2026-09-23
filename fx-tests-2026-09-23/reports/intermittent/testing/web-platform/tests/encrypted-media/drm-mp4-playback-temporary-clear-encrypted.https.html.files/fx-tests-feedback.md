## "How often does this WPT test time out, per config, and did the rate change at a landing?" — no WPT data

- Command: `fx-tests test testing/web-platform/tests/encrypted-media/drm-mp4-playback-temporary-clear-encrypted.https.html`
- Expected: per-config TIMEOUT/OK counts and `--history` for a WPT test (bug 1947049).
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: reused the `wptreport.json` dataset another agent had scraped for a sibling test (`../drm-mp4-playback-temporary.https.html.files/wptreport-status.json`), then a script to split rates by Treeherder push id around a landing. Same gap as that report's feedback.
- What would have answered it: WPT in `fx-tests test`, with a way to split `--history` at a revision (`--split-at <rev>`), since the question was "did bug 2071106 change the rate".

## `fx-tests intermittent --bug 1947049` default window hides all annotations

- Got: "no sheriff annotations ... between 2026-09-16 and 2026-09-22"; `--since 21 --tree all` showed 17. The annotations stopped because the expectation became `[OK, TIMEOUT]` on 2026-09-14, which the output cannot say.
- Would help: when the window is empty, print the date of the last annotation.
