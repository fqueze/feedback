## Question: which bug tracks this test's failures?

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_throttling_disable_unblocks_requests.js --bugs`
- Expected: Bug 2007038 ("Intermittent devtools/client/netmonitor/test/browser_net_throttling_disable_unblocks_requests.js | Test timed out -"), or at least a line saying none was found.
- Got: output identical to the run without `--bugs`: no bug section, no "none found" line.
- Workaround: Bugzilla REST `quicksearch=<test file name>`. (The bug has no sheriff annotations in the window, since every failure passed on the harness retry; `--bugs` may be keyed on annotations only, but then it should say so.)
