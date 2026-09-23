## Question: did the try push schedule the configs a removed skip-if covered

- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_rtl.js`
- Expected: next to the per-config table of runs, a line saying which of the test's skipped platforms (here mac, from its `skip-if`) had no job at all in the push.
- Got: the table lists only the 7 linux/windows configs that ran it. "No mac row" reads the same whether mac jobs ran and skipped the test, or were never scheduled.
- Workaround: `treeherder-cli <rev> --json` and grep the `platform` fields (0 mac jobs of 256).
