## Question: did the try push run the configuration the removed `skip-if` covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/storage/test/browser_storage_cache_overflow.js --all-jobs`
- Expected: next to the per-config table, a line saying which configs central skips this test on (`fx-tests test --coverage` knows: `test-linux2404-64/debug-mochitest-devtools-chrome-http3`, 226 skips) and whether the push scheduled them. That is the whole point of a push that removes a `skip-if`.
- Got: the 7 configs that ran it, all passing. Nothing says the only skipped config (the http3 variant) was absent from the push, so "passed everywhere" reads as "the skip-if is no longer needed".
- Workaround: listed the push's job types from the Treeherder API (`/api/jobs/?push_id=...`) and checked for `http3` by hand; none were scheduled.
