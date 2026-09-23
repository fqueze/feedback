# fx-tests feedback

## Question: did the try push run any job on the configurations the removed skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/styleeditor/test/browser_toolbox_styleeditor.js`
- Expected: next to the configs where the test ran, the configs where central schedules it (or skips it) that are absent from the push, e.g. "test-macosx1500-aarch64/*: central skips it here (skip-if ...); this push ran no job on it". The skip-if being tested was mac-only, so "passed 21/21" answers a question nobody asked.
- Got: only the 7 Linux/Windows configs where it passed. Nothing says the push had no macOS jobs at all.
- Workaround: `treeherder-cli <rev> --json --match-filter all | jq '.jobs[].job.platform' | sort | uniq -c` (14 MB of JSON), then `fx-tests test <path> --coverage --limit 0` to see the mac configs are skipped on central.
