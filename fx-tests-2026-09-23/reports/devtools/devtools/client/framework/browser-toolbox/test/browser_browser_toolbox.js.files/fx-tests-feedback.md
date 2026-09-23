## Did the try push run the configs the removed skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox.js`
- Question: the removed `skip-if` was mac-only (`os == 'mac' && os_version == '14.70' && arch == 'x86_64'`, `... '15.30' && arch == 'aarch64'`). Did this push run the test on those configs at all?
- Expected: a line saying the push scheduled no mac job, while central schedules this test on 3 mac configs (all skipped) — `fx-tests test --coverage` already knows the latter ("mac 0/3 ran — scheduled here, but skipped on every config").
- Got: a table of 7 linux/windows configs, all passed. Nothing says mac is absent, so "passed everywhere" reads like the skip-if can go.
- Workaround: Treeherder API `api/jobs/?push_id=...` and counting platforms (no macosx job among 256).
