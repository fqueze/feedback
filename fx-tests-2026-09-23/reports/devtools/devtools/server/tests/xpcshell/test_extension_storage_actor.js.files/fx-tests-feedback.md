## `fx-tests test <path> --bugs` prints no bug section, and missed the bug named after the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_extension_storage_actor.js --bugs`
- Expected: bug 1980334 ("Intermittent devtools/server/tests/xpcshell/test_extension_storage_actor.js | Test timed out"), or at least an explicit "no bug found".
- Got: the same output as without `--bugs`, no bug section at all; `--json` has `annotatedBugs: []`. The bug has 3 annotations in the last 30 days, all on mozilla-esr140, which may be why it is not found, but nothing says so.
- Workaround: found the bug numbers in the manifest's `skip-if` comments and queried Bugzilla by hand.
