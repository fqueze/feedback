## Question: did the try push schedule the config the removed skip-if applied to?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/storage/test/browser_storage_basic_usercontextid_2.js --all-jobs --task-ids`
- Expected: next to the per-config pass table, the configs where central skips this test (here `test-linux2404-64/debug-mochitest-devtools-chrome-http3`, from `fx-tests test --coverage`) flagged as "not scheduled on this push", so a clean table is not read as "skip-if obsolete".
- Got: only the 7 configs that ran it, all passing; nothing says the one skipped config was absent.
- Workaround: `fx-tests test <path> --coverage --limit 0` to find the skipped config, then the Treeherder jobs API (`/api/jobs/?push_id=...`) to confirm no `-http3` job ran. `fx-tests try --config http3` is refused.
