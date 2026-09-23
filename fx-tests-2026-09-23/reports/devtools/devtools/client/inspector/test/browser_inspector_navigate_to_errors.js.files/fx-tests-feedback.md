## Question: did the try push schedule the config the removed skip-if applied to?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/test/browser_inspector_navigate_to_errors.js --task-ids`
- Expected: next to the per-config table, a note that the test's central skip-if config (`test-linux2404-64/debug-mochitest-devtools-chrome-http3`, known from `fx-tests test --coverage`) had no job on this push, so "0 failed" says nothing about it.
- Got: 7 configs, all passed, and no mention that the only config the skip-if covered was never scheduled. Easy to misread as "no longer fails".
- Workaround: listed the push's job types from the Treeherder API (`/api/jobs/?push_id=...`) and grepped for `http3`: none.
