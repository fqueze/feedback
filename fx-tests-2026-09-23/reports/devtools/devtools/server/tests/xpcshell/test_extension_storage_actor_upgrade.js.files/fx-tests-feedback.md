## `test --bugs` prints nothing about bugs

- Command: `fx-tests test devtools/server/tests/xpcshell/test_extension_storage_actor_upgrade.js --bugs`
- Expected: a "Bugs" section listing bugs naming the test, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs` (stdout and stderr), so "no bugs" and "flag ignored" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'` (found only 3 resolved bugs from 2020-2021).
