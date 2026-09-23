## `Issues (first failure per run)` shows a TEST-KNOWN-FAIL todo as the failure

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_urlbar_transitions.js`
- Expected: the first unexpected failure of each run. In all 31 runs that is `waiting for vsync to be disabled - timed out after 50 tries.`, which is where the test fails.
- Got: `22x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... urlbarView-url` (plus 7x and 2x variants). That message is `SimpleTest.todo`, logged as `TEST-KNOWN-FAIL` in the per-test profile. The job's resource-usage profile records it as a `TestStatus` marker named `FAIL` with no expected status, so it looks like a failure. It comes first only because a failing test dumps its buffered log, and the todo sits before the vsync check in that log.
- Cost: I first went after the a11y "hidden node" path in AccessibilityUtils.js, until the per-test profile showed `TEST-KNOWN-FAIL`.
- Workaround: `fx-tests task <id> --messages`, which lists every message of the test, then the per-test profile's `TEST-UNEXPECTED-FAIL` markers.

## Failure modes are not grouped when the message contains a UUID

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js`
- Expected: one row, `uncaught rejection: PageActions: No anchor node for _<id>_`, about 45x.
- Got: one `1x` row per extension UUID (`... _68216625-..._`, `... _4035d3d8-..._`, …, "37 more"). The failure mode behind almost every failure of that test is split into 40+ rows, and nothing leads the list.
- Workaround: read the rows and count them by eye.

## Chunk numbers differ between `test --task-ids` and `task`

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA`
- Expected: the same job name in both.
- Got: `test` lists `IT0S_eCQRJ-SkMnxZ3h6jA.0 test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` and `task` says `...-chrome-7`. Same for `VYlSTop-RXKVXW_jxzuPVw` (`-1` vs `-7`). No cost this time, but chunk numbers are how jobs get compared.
