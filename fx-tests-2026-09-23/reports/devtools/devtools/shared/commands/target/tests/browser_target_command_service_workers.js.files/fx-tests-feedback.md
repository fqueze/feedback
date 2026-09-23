## The bugs that name this test

- Command: `fx-tests test devtools/shared/commands/target/tests/browser_target_command_service_workers.js --bugs`
- Expected: bug 1781324 ("Intermittent devtools/shared/commands/target/tests/browser_target_command_service_workers.js | single tracking bug", NEW), and the resolved ones naming the test (1725052 dup, 1728689, 1734047).
- Got: the same output as without `--bugs`, with no bug section and no "none found" line either.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_target_command_service_workers.js"`.
