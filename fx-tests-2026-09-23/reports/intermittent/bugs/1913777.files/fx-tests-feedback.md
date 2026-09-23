## Question: which failure do most jobs annotated on this bug show?

- Command: `COLUMNS=400 fx-tests intermittent --bug 1913777 --limit 0 --full-messages`
- Expected: the "Failure messages, per annotated job" section to show the dominant message, `Unable to find any tests at the path(s):` (a wptrunner CRITICAL log line, no test attached), with its job count.
- Got: 17 message lines, all `maxInterStageShaderVariables ... assert_unreached` subtest failures, which come from only 3 of the 34 jobs (misstarred). The harness-level CRITICAL, in 26 of 34 jobs (29 counting the ccov ones), does not appear at all, so the output points at the wrong failure. Nothing says that jobs without a test-level failure line were left out.
- Workaround: downloaded all 34 `live_backing.log`s and grepped each one for `Unable to find any tests` and `MOZHARNESS_TEST_PATHS`.
- What would have answered it: count harness-level `CRITICAL`/`ERROR` lines (or the errorsummary `log` actions of level CRITICAL) as failure messages, or print a line like "N jobs had no test-level failure message", and show the failure message on each occurrence row.
