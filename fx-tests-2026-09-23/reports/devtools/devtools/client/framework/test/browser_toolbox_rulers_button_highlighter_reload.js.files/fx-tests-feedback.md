## `TEST-KNOWN-FAIL` (todo) counted as the first failure of a run

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_rulers_button_highlighter_reload.js`
- Expected: the Issues block ("first failure per run") lists only unexpected results; a
  `todo(false, ...)` logged as `TEST-KNOWN-FAIL` is not a failure.
- Got: issue 1, `174x FAIL handleEvent() was unable to perform a11y checks on hidden node: id:
  command-button-rulers ...`. That message is `a11yWarn` → `SimpleTest.todo(false)` in
  `AccessibilityUtils.js`, and the per-test profile shows it as `TEST-KNOWN-FAIL`. Its runs
  actually fail on the `uncaught rejection: Connection closed ...` that follows it (checked in 7
  jobs with `fx-tests task --messages`). The `messages` array in `--json` has the same 174. So
  more than half of this test's failures are filed under the wrong failure mode, and the real
  one looks like 155 runs instead of 329.
- Workaround: `fx-tests task <id> --messages` job by job, and read the profile's `TEST-*`
  markers.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test <path> --bugs`
- Expected: a line saying no annotated bug names the test.
- Got: the same output as without `--bugs`, with no bugs section; only `--json` showed
  `"annotatedBugs": []`.
- Workaround: `--json`, plus a Bugzilla REST search on the test name.

## Question: "which messages did each failing run have?"

- Command: `fx-tests test <path> --task-ids --issue 1`, then `fx-tests task <id> --messages` for each
- The question was whether every run filed under issue 1 also has the issue-2 message. The
  aggregates only keep each run's first message, so I had to check jobs one at a time. A
  per-issue "also seen in these runs: <other message> n/m" line would have answered it.
