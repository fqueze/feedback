# fx-tests feedback (requestStorageAccess-same-site-iframe.sub.https.window.js, WPT)

## Question: "how often does this WPT test fail vs pass on one config, over time?"

- Command: `fx-tests test testing/web-platform/tests/storage-access-api/requestStorageAccess-same-site-iframe.sub.https.window.js`
- Expected: per-config rates and `--history`, as for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: `classify.py` here. It walks Treeherder pushes for a date range, keeps
  the macosx1500 wpt jobs, fetches each job's `wpt_errorsummary.log`, checks whether
  `/storage-access-api` is in its `groups`, and whether the test has unexpected
  results. That took 140 runs and about 25 minutes.
- What could have shown it: WPT support in `fx-tests test`. The errorsummary `groups`
  line alone identifies which chunk ran the test, and absence from the errors means
  it passed as expected.

## Question: "which annotated jobs are this test's failure, and which were starred on the tracking bug for something else?"

- Command: `fx-tests intermittent --bug 1985680 --since 30 --tree all`
- Expected: each occurrence's failure line.
- Got: `lines` is empty for 181 of 189 occurrences. The 8 lines it did show belong to
  other tests (storage-access-headers, a reftest), so the summary's "Failure messages"
  block points at the wrong test.
- Workaround: read `wpt_errorsummary.log` for each job.
- What could have shown it: the job's unexpected results for the test the bug names,
  from the errorsummary, when Treeherder has no stored line.
- The default `--tree trunk` window also returned nothing, although all 45 of the
  last 7 days' annotations are on mozilla-beta. The message did say to try `--tree all`.
