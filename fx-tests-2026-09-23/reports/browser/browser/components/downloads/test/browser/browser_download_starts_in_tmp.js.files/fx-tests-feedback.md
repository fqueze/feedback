## "Does this test fail only in jobs where the test before it failed?" has no direct answer

- Question: are all 18 failing jobs of `browser_download_starts_in_tmp.js` jobs where `browser_download_spam_protection.js` (the test before it in the manifest) failed too, and which of the latter's failing jobs did not cascade?
- Command: `fx-tests test <path> --task-ids --limit 0` for both tests, then `grep`/`comm` on the task IDs.
- What its output could have shown: for each failing task, the tests that failed before it in the same job (the co-failure view `fx-tests task` gives for one job), or a `--co-failing <other path>` count over the window.
