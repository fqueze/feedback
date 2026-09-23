## `fx-tests test <path>` Issues split one failure mode into three in debug jobs

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_ctrlTab.js`
- Expected: "Issues (first failure per run)" to put each run under the failure that happened first in it.
- Got: 696x "Every preview should have a thumbnail", 133x "leaked 1 window(s) until shutdown", 18x "leaked 1 docShell(s)". The leak rows are all debug jobs, and their task sets are disjoint from issue 1's, which reads as two independent failure modes. Opening one of them (`fx-tests task ZkDufv1rQBOaKGTK9SI_dw --messages`) shows the same run also has "Every preview should have a thumbnail" and the "Test timed out" that follows it: the shutdown leak, reported last, is listed as the run's first failure.
- Workaround: `--task-ids --issue N` for each issue, `comm` the task sets, then `fx-tests task --messages` on a leak job.
- Would have helped: order a run's messages by time (the shutdown leak is always last), or show under each issue how many of its runs also carry another issue's message.
