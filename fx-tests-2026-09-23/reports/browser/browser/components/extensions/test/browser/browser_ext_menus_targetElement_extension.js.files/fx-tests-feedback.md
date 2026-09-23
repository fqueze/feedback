## Which subtest timed out, per failure

- Question: which `add_task` of the test timed out in each failing run (this test has two failing subtests, `getTargetElement_in_extension_tab` and `getTargetElement_in_extension_tab_on_click`, same mechanism).
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_menus_targetElement_extension.js`
- Expected: the Issues list to split `TIMEOUT` by the subtest named in `TEST-FAIL | <path> | <subtest> - Test timed out`.
- Got: a single row `114x TIMEOUT Test exceeded time limit`; the subtest name only appears in the task logs.
- Workaround: downloaded `live_backing.log` per task and grepped `TEST-FAIL`.
