# fx-tests feedback (browser_update_interactive_noprompt.js)

## A `todo` (known fail) is reported as the failure of every run

- Command: `fx-tests test browser/base/content/test/browser-webextensions/browser_update_interactive_noprompt.js` (same on the old `webextensions/` path, and on `browser_update_checkForUpdates.js`)
- Expected: `Issues (first failure per run)` naming the unexpected failure, `Shutdown - leaked window until shutdown [url = about:addons]`.
- Got: `24x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: BUTTON, className:` as issue 1 for every run. That message is `SimpleTest.todo()` from `AccessibilityUtils.js` (`a11yWarn`): status FAIL, expected FAIL, printed `TEST-FAIL` (not `TEST-UNEXPECTED-FAIL`), counted under `Todo: 2` in the browser-chrome summary, and logged in every run of the test, passing ones included. The unexpected failure only shows with `fx-tests task <id> --messages`.
- Cost: I started out reading AccessibilityUtils.js to explain an a11y failure that is not a failure.
- Workaround: `fx-tests task <id> --messages` on each task.
- Suggestion: skip results whose `expected` equals their `status` when picking "first failure per run", or label them `todo`.

## Question: "the retention path of each `leaked window until shutdown` failure"

- The message now carries its useful part in `stack` (the CC retention path from `ShutdownLeakPathFinder`), and the path is what tells the failures apart. `fx-tests task <id> --messages` prints the message only.
- What I did: downloaded `live_backing.log` for 10 tasks and grepped 11 lines after `leaked window until shutdown`. For a failure whose retry passed, `mochitest-browser-chrome_errorsummary.log` does not have it (the group is `OK`).
- What would have answered it: `fx-tests task <id> --messages --stacks`, or the stack in `fx-tests test <path> --task-ids --full-messages`.
