## Question: which test failed first, in the same browser, in each failing job of a victim test

- Command: `for t in <14 task ids>; do fx-tests task $t --messages; done`, then awk to pull out, per job, whether `browser_feature_callout_in_chrome.js` had `sendTriggerMessage which is already wrapped` and whether `browser_coenrolling_messaging_features.js` failed.
- Expected: `fx-tests test <path>` to say, per failing job, which tests of the same manifest failed before this one (the leaker suspects), e.g. a `--preceding-failures` column, since an "already wrapped" victim is the common case.
- Got: only per-job views; 14 separate `task` calls plus a script.
- Workaround: the loop above.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_trigger_messagesLoaded.js --bugs`
- Expected: an explicit "No bug names this test" line.
- Got: the normal output with no bugs section at all, on stdout and stderr, so "no bug" looks like "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<name>` to confirm.
