## TEST-KNOWN-FAIL (todo) messages are counted as failures, and even ranked as a run's first failure

- Command: `fx-tests test accessible/tests/browser/windows/uia/browser_textPatterns.js` (Issues list), then `fx-tests task NmtPsNfAQE6gijVTUlFwmA.0 --messages`
- Expected: issue 4 ("Wrong width of text between offsets (0, -1) ... - Got 22 Expected 22", 2x) to be a real failure mode; the job's messages to list only unexpected results.
- Got: in the job's profile every "Wrong width ..." message is `TEST-KNOWN-FAIL` (a `todo`). The job's real failures are "range is at top of document - Got 85, expected +0" and "range is at bottom of document - Got 976, expected +0", which come later in the log. Because the known-fails come first, the whole run is filed under issue 4, and "Got 22 Expected 22" reads like a nonsensical failure. The same known-fail messages also inflate the "+8 more messages" of every TextChanged-timeout job.
- Workaround: read the test log in the per-test profile (`thread markers --category Test --search <file> --list`) and filter on TEST-UNEXPECTED.
