## gtest failures are invisible to `fx-tests test` and `fx-tests task`

- Command: `fx-tests test dom/media/gtest/TestAudioTrackGraph.cpp` (also `fx-tests test TestAudioTrackGraph.SecondaryOutputDevice`)
- Expected: rates per config for a gtest that fails ~1.4% on macOS, or a message saying gtest is not covered.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — reads as if the test did not run, when the harness is simply not indexed.
- Workaround: Treeherder `api/project/autoland/jobs/?job_type_name=test-macosx1500-aarch64/debug-gtest-1proc` for run counts, and job logs for the failures.

- Command: `fx-tests task fk_eMGIiQNmiBvgIq-QNtQ --profiles`
- Expected: the failing gtest listed (it is in the job's resource-usage profile as a `test` marker with Status FAIL and a `FAIL` marker carrying the assertion text).
- Got: "0 tests, 0 executions, 0 failing" and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". The warning line does name `FAIL TestAudioTrackGraph.SecondaryOutputDevice`, contradicting the summary.
- Workaround: loaded the resource-usage profile with profiler-cli; `thread markers --search SecondaryOutputDevice --list` finds the test and its failure.

## `fx-tests intermittent --bug` only covers 7 days

- Question: "how long has this bug been failing, on which platforms, with which value" — needed the bug's whole history (122 annotations since 2026-05-28).
- Command: `fx-tests intermittent --bug 2003533` (8 annotations, 7 days).
- Workaround: `https://treeherder.mozilla.org/api/failuresbybug/?startday=2025-11-01&endday=2026-09-23&tree=all&bug=2003533`.
