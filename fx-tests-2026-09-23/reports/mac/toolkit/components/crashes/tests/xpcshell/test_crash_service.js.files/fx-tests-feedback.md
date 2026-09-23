# fx-tests feedback — test_crash_service.js

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test toolkit/components/crashes/tests/xpcshell/test_crash_service.js --bugs`
- Expected: a "Bugs" section, or a line saying no open bug names this test.
- Got: exactly the same output as without `--bugs`, with no sign the flag did anything. `--json` has `annotatedBugs: []` and no `bugs` key.
- Workaround: checked the JSON, then searched Bugzilla directly (`/rest/bug?summary=test_crash_service.js`). That found 6 closed bugs, none open.
