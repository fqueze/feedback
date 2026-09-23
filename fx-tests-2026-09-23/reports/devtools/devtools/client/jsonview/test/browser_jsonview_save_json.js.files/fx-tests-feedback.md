## `test --bugs` prints no bugs section when none is found

- Command: `fx-tests test devtools/client/jsonview/test/browser_jsonview_save_json.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`; nothing says whether a lookup ran and found nothing.
- Workaround: Bugzilla REST quicksearch for the file name (`{"bugs":[]}`).
