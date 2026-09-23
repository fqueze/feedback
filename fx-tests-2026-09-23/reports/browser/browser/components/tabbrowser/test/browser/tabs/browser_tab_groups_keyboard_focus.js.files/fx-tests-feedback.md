## `--bugs` did not find the test's tracking bug

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups_keyboard_focus.js --bugs`
- Expected: bug 1947600, "Intermittent browser/components/tabbrowser/test/browser/tabs/browser_tab_groups_keyboard_focus.js | single tracking bug" (REOPENED), which names the test in its summary.
- Got: the normal `test` output, with no bug section at all, not even "no bugs found".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_tab_groups_keyboard_focus&include_fields=id,summary,status"`.
