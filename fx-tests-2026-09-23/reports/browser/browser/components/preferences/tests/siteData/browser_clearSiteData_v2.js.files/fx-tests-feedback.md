## `test --bugs` prints no bug section at all
- Command: `fx-tests test browser/components/preferences/tests/siteData/browser_clearSiteData_v2.js --bugs`
- Expected: the bugs naming the test (Bugzilla has 1890595, 1894578, 2019398 "single tracking bug", all resolved), or an explicit "none".
- Got: the same output as without `--bugs`, no bugs block, no "none found" line.
- Workaround: Bugzilla REST `bug?summary=browser_clearSiteData_v2`.

## Question: "a passing job of the same config, to compare its resource-usage profile"
- Command: `fx-tests test <path> --profiles --config test-linux2404-64-asan/opt-mochitest-browser-chrome-swr`
- Got: resource-usage URLs for failing jobs only; no way to list task IDs of jobs where the test passed.
- What could have shown it: `--task-ids --passes` (a few passing task IDs per config), to check whether the same disk-writeback burst also occurs in passing jobs.

## Per-config Issues disagree with the unfiltered view
- `fx-tests test <path>` lists 27x "Fail to load service worker test"; with `--config test-linux2404-64-asan/opt-mochitest-browser-chrome-swr` the 7 failures show as 4x "Failure details not recorded (likely Android or platform logging issue)" + 3x the SW message, although the unfiltered view attributes all 27 to the SW message.
