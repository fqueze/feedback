## Question: which crash is behind a reftest bug's "incomplete after application is no longer top", and since when (bug 2066634)

- `fx-tests test layout/reftests/svg/as-image/img-widthAndHeight-meet-1.html` -> exit 2, "No test path in the xpcshell and mochitest 21-day data". Reftests are not indexed, so there is no per-config rate, history or coverage for them.
- `fx-tests intermittent --bug 2066634` gives only the 7-day window (14 jobs) and the harness message, which is the same for every crash. Needed: the crash reason (`Mozilla crash reason` / MOZ_ASSERT text) per occurrence, and the first/last date per reason over the bug's whole life.
- Workaround: Treeherder `api/failuresbybug/?bug=2066634&startday=...` (137 jobs since 2026-08-21), then downloading each task's `reftest-qr_errorsummary.log` and grepping `PROCESS-CRASH` lines out of each `live_backing.log` (~134 logs, a few minutes). The errorsummary `crash` entry has the signature but not the MOZ_ASSERT reason, so `@ RecvPDocAccessibleConstructor` could not be split into its two assertions without reading the line number off the stack and the source.
- What could have answered it: `intermittent --bug N --since <date>` over the bug's whole life, grouped by crash reason, with first/last push date per group.

## `fx-tests task WVXkTMAAQsSbOwyYYKs8cQ --profiles`: expected-random fail reported as a failing marker

- Got: `warning: 1 failing marker in this job named no test path ...: FAIL layout/reftests/svg/as-image/img-and-image-1.html == ...`. In the log that test is `TEST-KNOWN-FAIL(EXPECTED RANDOM)`, so it did not fail.
- Expected: known/expected-random failures left out of the failing-marker warning.
