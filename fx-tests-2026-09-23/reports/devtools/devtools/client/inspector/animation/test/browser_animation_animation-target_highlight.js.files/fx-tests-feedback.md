## A TEST-UNEXPECTED-PASS failure is listed with no message

- Command: `fx-tests task H6RcZP1URlSmjnjo-6UreA --messages --full-messages` (same in `fx-tests try 2888bcab0070 --limit 0 --messages`)
- Expected: the failing test's message, `We expect at least one assertion to fail because this test file is marked as fail-if in the manifest.`, or at least "unexpected pass (fail-if)".
- Got: `FAIL — 2 failing executions of 2` and no message at all, so the failure mode is invisible from fx-tests. The resource-usage profile records that line as a green `PASS` TestStatus marker (`m-91` in TjpNuGcpSXiGpDTW8XJNqQ's resource-usage profile), so a reader filtering on FAIL misses it.
- Workaround: load the per-test profile and list its `Test` category markers; the per-test profile has it as `TEST-UNEXPECTED-PASS`.

## `fx-tests try <rev> --test <path> --task-ids --profiles` ignores --task-ids and --profiles

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/inspector/animation/test/browser_animation_animation-target_highlight.js --task-ids --profiles`
- Expected: the per-config table plus the failing task IDs and profile URLs (the question: "the failing tasks of this test on this push").
- Got: only the per-config table. Without `--test`, the default output truncates to 10 rows and this test was not among them.
- Workaround: `fx-tests try <rev> --task-ids --profiles --limit 0` and search the output for the test name.
