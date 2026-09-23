# fx-tests feedback (browser_startup_flicker.js)

## `todo()` messages (TEST-KNOWN-FAIL) reported as the failure

- Command: `fx-tests test browser/base/content/test/browser-performance/browser_startup_flicker.js`
- Expected: Issues rows naming the assertion that failed (`unexpected changed rect: ...`,
  `should have 0 unknown flickering areas`).
- Got: the top FAIL row (21 of 25, and 60 of 82 at the old path) is
  `bug 1445161 - the window should be focused at first paint, []`, and row 3 is
  `urlbar focus ring settles during startup idle tasks (bug 2066735), ...`. Both are
  `todo(false, ...)` calls in the test, i.e. expected failures that the test logs on every run,
  passing ones included. The real failure messages only appear in `fx-tests task <id> --messages`.
  The Issues block therefore groups failures by a message that carries no information, and
  splits them into rows by the text of a todo.
- Workaround: `fx-tests task <id> --messages` for every failing task (one call per task, ~100
  tasks here), then grouping by hand.

## Question: "what are the real failure messages across all failing runs of this test"

- Command used: `fx-tests test <path> --task-ids --json` only carries the first message per
  task, so I looped `fx-tests task <id> --messages` over every task.
- What the output could have shown: every distinct message per failing run (or at least the
  first non-todo one), so that the failure modes can be counted without one call per task.

## "Passed when the harness reran it" missing from the per-test block

- Command: `fx-tests task fLb07b9NTgOfTuc6-7WduA.0 --messages` (two tests failed in the job)
- Expected: each test's block to say whether its retry passed, as it does when only one test fails.
- Got: the per-test block only says `FAIL — 1 failing execution of 2`; the retry outcome is in a
  header line `All 2 passed when the harness reran them.` Parsing per-test blocks across ~100
  tasks, I first read those 25 as "retry failed".
- Workaround: read the header line as well.
