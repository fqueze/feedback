## Which subtest timed out, and what the test logged just before the timeout, per failing run

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_menus_targetElement_extension.js` (and `--task-ids --limit 0`)
- Expected: the failure modes under Issues to separate by subtest and by the console error preceding the timeout. Here every timeout is preceded by `TypeError: doc is null` (ContextMenuChild.sys.mjs:593), and the subtest differs (`getTargetElement_in_extension_tab` vs `getTargetElement_in_extension_tab_on_click`); the sheriff annotation text carries the subtest name (`getTargetElement_in_extension_tab_on_click - Test timed out`).
- Got: a single row, `115x TIMEOUT Test exceeded time limit`, with no subtest and no preceding error.
- Workaround: loaded 9 per-test profiles one at a time and listed the Test markers of each, to learn that 6 of 9 were in the `_on_click` subtest, 3 of 9 in the first one, and all 9 had `doc is null` first. Several minutes of profile loading for what a column could have said.
