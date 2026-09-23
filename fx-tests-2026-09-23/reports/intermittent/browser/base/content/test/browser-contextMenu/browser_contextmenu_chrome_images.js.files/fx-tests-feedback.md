## Renamed test reads as healthy
- Command: `fx-tests test browser/base/content/test/browser-contextMenu/browser_contextmenu_chrome_images.js` (and `--history`)
- Expected: a hint that the test was renamed from `browser/base/content/test/contextMenu/` on 2026-09-16/18, or history merged across the rename.
- Got: "Verdict: passing. 895 of 895 runs passed", with history starting 2026-09-16 and nothing about the old path. All 88 failures are only under the old path. I found that out from `fx-tests intermittent --bug`, whose summary uses the old path.
- Workaround: run `fx-tests test` again on the old path.

## Question: which subtest timed out, per run
- Command: `fx-tests test <old path> --task-ids --limit 0`
- Issues shows only `88x TIMEOUT Test exceeded time limit`. The failing subtest (`test_context_menu_items_for_allowed_chrome_images` or `test_save_allowed_chrome_image`) and the `TEST-UNEXPECTED-FAIL | <subtest> - Test timed out` line that comes before it are not shown. `intermittent --bug` has the split only for starred jobs, most of them on esr153.
- Workaround: downloaded 17 live_backing.log files and grepped them.
- What would have answered it: group the Issues of a timeout by its `<subtest> - Test timed out` message.
