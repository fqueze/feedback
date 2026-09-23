## Question: was the config the removed skip-if covers scheduled on this try push at all?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_response_CORS_blocked.js`
- Expected: next to the per-config ran/pass table, a line naming the configs where central skips this test (here `test-linux2404-64/debug-mochitest-devtools-chrome-http3`, 226 skips) and whether the push scheduled them.
- Got: only the 7 configs that ran it, all passed. Nothing says the one config that mattered was never scheduled.
- Workaround: listed the push's job types from the Treeherder API (`/api/jobs/?push_id=...`) with a Python one-liner, and matched them against `fx-tests test --coverage`.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_response_CORS_blocked.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the normal `test` output with no bugs section, so I could not tell "none found" from "flag ignored".
- Workaround: took it as none.
