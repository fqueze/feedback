## Question: "did the try push run the config the removed skip-if applied to?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/netmonitor/test/browser_net_reporting_api.js --all-jobs`
- Expected: next to the per-config table, the test's `skip-if` configs on central (`fx-tests test --coverage` shows `test-linux2404-64/debug-mochitest-devtools-chrome-http3 ... skipped`) and whether the push ran them. Here the only skipped config, the `-http3` variant, was not scheduled at all, so 21/21 passes say nothing about the removed skip.
- Got: only the configs that ran; nothing says the skipped config is missing.
- Workaround: `fx-tests test <path> --coverage --limit 0` for the skipped config, then `treeherder-cli <rev> --json` and a script over job names to check it was never scheduled.
