## Question: which message is the failure of this test?

- Command: `fx-tests test browser/components/urlbar/tests/browser-searchMode/browser_searchModeSwitcher_telemetry.js` (Issues), and `fx-tests task <id> --profiles --messages`
- Expected: the first TEST-UNEXPECTED-* message, i.e. `searchMode should exist on moz-urlbar - true == false` (or `Uncaught exception in test - undefined - timed out after 50 tries.`).
- Got: `54x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... searchmode-switcher-close ...` as "first failure per run". In all three profiles read (a11y-checks, swr-uipc, windows standalone) that message is `TEST-KNOWN-FAIL` (Status FAIL, Expected FAIL: an AccessibilityUtils `todo`), not a failure. `--messages` also lists it among the failures with no expected/unexpected distinction.
- Workaround: read the test log in the profile (`thread markers --category Test --search <test> --list`) to find the TEST-UNEXPECTED-FAIL lines.
- What the output could show: skip status==expected messages when picking the first failure, or mark them `(known-fail)` in `--messages`.

