## Question: did the try push exercise the config the test is skipped on?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/filters/browser_net_filter-03.js`
- Expected: next to the per-config pass counts, a line saying that the config where central skips this test (`test-linux2404-64/debug-mochitest-devtools-chrome-http3`, 188 skips, reason `http3`) was not in the push, or ran and skipped it again.
- Got: 7 configs, all passed. Nothing said that the only config the skip covers was absent. That looks like "the skip can go" when the skip was never tested.
- Workaround: `fx-tests test <path> --coverage --limit 0` to find the skipped config, then the push's Treeherder job list to check that no http3 job was scheduled.
