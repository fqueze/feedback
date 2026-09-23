## Question: "which bugs name this test?"

- Command: `fx-tests test devtools/client/storage/test/browser_storage_cache_navigation.js --bugs`
- Expected: the bugs naming the test, or an explicit "no bug names this test" line.
- Got: the ordinary `test` output with no bug section at all; `--json` has `annotatedBugs: []`. Bugzilla has several bugs with the file name in their summary (1767738, 1894899, 1712937), all closed.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_storage_cache_navigation"`.
- What could have shown it: a "Bugs" section that says "none open; closed: ..." or "none", so an empty answer is not confused with a flag that did nothing.
