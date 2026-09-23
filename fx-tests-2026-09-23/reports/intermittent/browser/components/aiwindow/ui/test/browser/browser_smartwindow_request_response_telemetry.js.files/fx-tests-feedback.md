## Question: on which trees and versions does each failure message of a bug occur?

- Command: `fx-tests intermittent --bug 2030788 --since 21 --tree all --limit 0`
- Expected: the "Failure messages" block broken down by tree/platform, since that is where a single tracking bug's modes separate.
- Got: messages, trees and platforms as three independent histograms. 52 of 60 annotations were `records offline ... Request timed out.` on mozilla-esr153/mozilla-release, invisible in `fx-tests test` (central/autoland only). Tying message to tree needed a script over `--json` `occurrenceRows[].lines`.
- Workaround: python over `--json`; then the Treeherder `failuresbybug` API directly for dates before the window, and each build task's `target.json` for the Firefox version.
- Could have shown: a message x tree table, and the app version per occurrence.

## `fx-tests test <path>` does not say the bug's main failure mode lives on other trees

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js`
- Expected: a hint when sheriff annotations for the test's bug are dominated by trees this data does not cover.
- Got: only trunk issues (OOM crashes on win32 etc.); nothing hinted that ESR153 fails nearly every Windows shippable run.
- Workaround: `fx-tests intermittent --bug N --tree all`.

## `fx-tests test <path> --bugs` printed nothing extra

- Command: `fx-tests test <path> --bugs`
- Expected: the sheriff-annotated bugs naming the test.
- Got: the normal output with no bugs section, no error on stdout.
