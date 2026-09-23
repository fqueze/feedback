## `fx-tests test` reports a TEST-KNOWN-FAIL todo as the failure mode

- Command: `fx-tests test devtools/client/inspector/compatibility/test/browser/browser_compatibility_settings.js`
- Expected: "Issues (first failure per run)" to show the unexpected failure, `waiting for vsync to be disabled - timed out after 50 tries. - false == true`.
- Got: `FAIL handleEvent() was unable to perform a11y checks on hidden node: ... compatibility-footer__button`. That message is `SimpleTest.todo()` (a11yWarn in AccessibilityUtils.js); the per-test profile logs it as TEST-KNOWN-FAIL. `fx-tests task <id> --messages` likewise lists 24 of these todos as failure messages for the test, ahead of the 2 real ones. The resource-usage profile's `FAIL` TestStatus markers carry no expected/unexpected distinction, which is probably where it comes from.
- Workaround: read the test's log in the per-test profile (`--category Test`), where TEST-KNOWN-FAIL and TEST-UNEXPECTED-FAIL are distinct.

## Question: "the minidump of this job's crash"

- Command: `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ --profiles` (and `--json`)
- Expected: the CRASH row (`@ mozilla::(anonymous namespace)::RunWatchdog`, browser_compatibility_unsupported-browsers_some.js) to carry its minidump ID, so `fx-tests crash <task> <id>` can follow.
- Got: no minidump ID in text or JSON; `fx-tests test <that test> --task-ids` shows none either (and counts 0 crashes).
- Workaround: found `7b1314b8-f89a-42eb-d98c-6775f5d5372f` in the resource-usage profile's `output` markers ("ExceptionHandler::GenerateDump attempting to generate: .../minidumps/7b1314b8-....dmp"), then `fx-tests crash Xxr5rCPkSwCY8T4P8nONtQ 7b1314b8-f89a-42eb-d98c-6775f5d5372f --all-threads` worked. The job also uploaded `profile_shutdown_hang_7839.json`, which `--profiles` does not list.
