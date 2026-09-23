## Question: "which of this test's TIMEOUTs are the same failure?"

- Commands: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marAppInUseSuccessComplete.js` (Issues: `44x TIMEOUT Test exceeded time limit`), then `fx-tests task <taskId> --messages` on several failing tasks.
- Expected: some way to tell the timeouts apart.
- Got: every run reads `Test timed out`, and so does `--messages`. From the resource-usage profiles, the 44 timeouts are at least three different failures: the test stuck polling `checkCallbackLog` (the callback app never started), an uncaught `NS_ERROR_FILE_ACCESS_DENIED [nsIFile.permissions]` in `setupUpdaterTest`, and on Linux ASan an ASan abort in the helper binary.
- Workaround: I loaded each failing job's resource-usage profile, one at a time, and grepped the test's replayed log (~1 min per job).
- Could have shown: for a TIMEOUT, the last test log line before the timeout, or the first `JavaScript error`/`console.error` in the replayed log, grouped like the Issues list.
