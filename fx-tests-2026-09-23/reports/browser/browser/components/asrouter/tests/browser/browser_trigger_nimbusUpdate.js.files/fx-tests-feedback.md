## `test --bugs` fails on a test that is in its manifest

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js --bugs`
- Expected: the bugs naming the test, or "none".
- Got: the usual summary, then `error: browser/components/asrouter/tests/browser/browser_trigger_nimbusUpdate.js@2981cf3b1d3f5ff2fa940554e0faea2d2db15073: not found in manifest`. The test is listed in `browser/components/asrouter/tests/browser/browser.toml` at the failing autoland revision and in a current local checkout. The manifest lookup for that revision seems to be what fails (hg.mozilla.org also returns 404 for `mozilla-central/raw-file/tip/...` right now).
- Workaround: searched Bugzilla directly with `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_trigger_nimbusUpdate'`.

## Question: "is every failing job of test A also a failing job of test B?"

- Needed to show that this victim fails exactly when the leaker fails in the same (non-standalone) job.
- Commands: `fx-tests test <victim> --task-ids --limit 0` and `fx-tests test <leaker> --task-ids --limit 0`, compared by hand.
- Output that would have answered it: an option on `test` taking a second path (or `--co-failing`) that lists the jobs where both failed, where only one failed, and the config of each.
