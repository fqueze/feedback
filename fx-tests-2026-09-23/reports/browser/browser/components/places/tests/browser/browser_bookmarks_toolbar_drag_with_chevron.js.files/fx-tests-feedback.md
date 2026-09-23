## `test` Issues and `task --messages` count a TEST-KNOWN-FAIL (todo) as a failure

- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarks_toolbar_drag_with_chevron.js` (and `fx-tests task WrurztgRR8ux0eLxC5Chvw --messages`)
- Expected: "Issues (first failure per run)" to list the first TEST-UNEXPECTED-* message of each run.
- Got: issue 1 (26x) is `handleEvent() was unable to perform a11y checks on hidden node: id: PlacesChevron ...`, which is emitted by `a11yWarn` -> `SimpleTest.todo(false, ...)`, i.e. a TEST-KNOWN-FAIL (the per-test profile marks it `TEST-KNOWN-FAIL`). In all 26 of those runs the real first failure is `uncaught exception - Error: event target of "mousedown" is not srcElement nor its descendant`, so the true split is 36x that message + 1x a11y "Node is not accessible", not 26/10/1. `task --messages` also lists the todo among the failing messages.
- Workaround: read the per-test profile's `--category Test` markers to see which message is TEST-KNOWN-FAIL.

## Question: "a profile holding a passing run of this test on a config where it never fails"

- Commands: `fx-tests issues --harness mochitest --path browser/components/places/tests/browser --group-by test`, then `fx-tests test <other test> --task-ids` filtered with rg for non-standalone windows11 jobs, then `fx-tests task aFfiSkiRTEev9FAjnmKcog --profiles`.
- Why: the test fails only on standalone configs. A per-test profile of a later test in the same manifest and job (browser_bug485100-change-case-loses-tag.js) covers the whole browser session, so it contained a passing run of this test. That run was decisive: same race, fewer bookmarks.
- What would have answered it: something like `fx-tests test <path> --passing-profiles [--config ...]`, listing failure profiles of tests that ran after <path> in the same browser session, in jobs where <path> passed.
