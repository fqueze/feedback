## Question: "Did the try push run the config the removed skip-if targeted?"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/storage/test/browser_storage_localstorage_error.js`
- Expected: next to the per-config pass table, a line saying the configs where central skips this test (here only `test-linux2404-64/debug-mochitest-devtools-chrome-http3`, from `fx-tests test --coverage`) had no job on the push.
- Got: only the configs that ran. Without cross-checking `--coverage` by hand, "21/21 passed" reads as "the skip can go".
- Workaround: listed the push's job types from the Treeherder API (`/api/jobs/?push_id=...`) with a Python script and checked for `-http3` myself.
