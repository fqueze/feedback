## Question: did the try push schedule the config my skip-if covers?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/filters/browser_net_filter-sts-search.js`
- Expected: next to the per-config table, the configs where the test is skipped on central (from `fx-tests test <path> --coverage`, here `test-linux2404-64/debug-mochitest-devtools-chrome-http3`) marked "not scheduled on this push".
- Got: only the 7 configs where it ran. A config the push never scheduled looks the same as one that was never relevant.
- Workaround: a Python script over the Treeherder job list (`todo.files/try-jobs.json`) to grep job names for `http3`.
