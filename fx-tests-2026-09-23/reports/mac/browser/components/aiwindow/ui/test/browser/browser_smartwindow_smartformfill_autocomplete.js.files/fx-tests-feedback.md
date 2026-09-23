## `test --bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartformfill_autocomplete.js --bugs`
- Expected: a line such as "Annotated bugs: none" when the Treeherder/Bugzilla query finds nothing.
- Got: the same output as without `--bugs`, so I couldn't tell "no bugs" from "the live query failed or the flag was ignored". Only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json`, then a Bugzilla summary search, which found bug 2068789 (a test-verify timeout, RESOLVED FIXED).

## `test --config` loses the failure message

- Command: `fx-tests test <path> --config macosx1500`
- Expected: the same Issues rows as without the filter, restricted to Mac: `TypeError: can't access property "textContent", row.renderRoot.querySelector(...) is null`.
- Got: `10x FAIL Failure details not recorded (likely Android or platform logging issue)` for those same 10 Mac failures. Without `--config` they are listed under the TypeError message.
- Workaround: `--task-ids --issue 2` without `--config`, then reading the config off each task.

## Question: which subtest timed out, in each failing job

- Command: `fx-tests test <path> --task-ids --issue 3` (TIMEOUT "Test timed out")
- The message drops the subtest name that the log's line carries (`TEST-FAIL | <path> | <describe> / <it> - Test timed out`). Here the subtest separated two mechanisms: 16 Mac/Windows timeouts were in subtests 2 and 4 (focus stolen by the AI Window smartbar), and 5 Linux ones were in subtest 3 (login autofill).
- What I did: curled 19 `live_backing.log`s and grepped them for that line.
- What would have answered it: an Issues breakdown, or `--task-ids` rows, carrying the subtest name for timeouts.
