## Question: which sub-test hung, in each occurrence of a MediaTestManager "Test timed out!"

- Command: `fx-tests test dom/media/test/test_eme_waitingforkey.html --task-ids --limit 0`
- Expected: some way to see the line that discriminates occurrences. For dom/media tests the failure is always `Test timed out!`, and the next log line, `INFO - <token> timed out!`, names the sub-test that hung.
- Got: 16 identical `Test timed out!` rows. Android jobs have no per-test profile, so the only way to tell them apart was to load 16 resource-usage profiles one by one and search their markers (about 10 minutes).
- What the output could show: the first INFO line following the failure (or a `--context N` option), so occurrences can be grouped by sub-test without loading profiles.
