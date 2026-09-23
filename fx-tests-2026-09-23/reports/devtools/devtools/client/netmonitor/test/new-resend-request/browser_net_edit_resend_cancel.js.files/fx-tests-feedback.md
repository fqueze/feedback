# fx-tests feedback (browser_net_edit_resend_cancel.js)

## Question: which job ran this test and passed, so I can read that job's resource-usage profile?

- Command: `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_edit_resend_cancel.js --task-ids`
- Expected: for a test with 0 failures in 4,170 runs, some way to get a task ID of a passing run (`--coverage --task-ids`, or one sample task per config), since a passing test leaves no per-test profile and the resource-usage profile of a job that ran it is the only runtime evidence.
- Got: `--task-ids` only lists tasks behind failures; there are none, so nothing is printed. `--coverage` gives counts per config but no task.
- Workaround: loaded a sibling test's per-test failure profile from the try push (ct9m-aJqRqGxz_JUAX_2mQ, `browser_net_new_request_panel_context_menu.js`), which covers the same browser session and so includes this test's run.

## `Issues (first failure per run)` lists a `run-if` exclusion

- Command: `fx-tests test <this test>`
- Got: `1.   188x  SKIP    http3` under "Issues", for a manifest-wide `run-if = ["!http3"]` (formerly `skip-if = ["http3"]`, converted by bug 2072388 on 2026-09-17). The guide says `run-if` is scoping, not a disabled test, but the Issues block does not tell the two apart, and the per-day history shows the skips dropping to 0 from 2026-09-18 without saying why (the aggregates drop `run-if` skips).
- Expected: the Issues row, or the history, to say the skip is a `run-if` (or that the skip was converted), so a reader does not treat it as a disabled test.
