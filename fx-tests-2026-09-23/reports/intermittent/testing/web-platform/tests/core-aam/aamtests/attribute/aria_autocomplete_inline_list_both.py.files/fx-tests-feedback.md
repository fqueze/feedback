## Question: failure rate and runs of a WPT test (aamtest)

- Command: `fx-tests test testing/web-platform/tests/core-aam/aamtests/attribute/aria_autocomplete_inline_list_both.py` (also `--history`)
- Expected: per-config pass/fail counts, or at least a message that WPT is not covered and where to look.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — reads as a misspelled path.
- Workaround: Treeherder `jobs/<id>/similar_jobs/?count=200` for the job's history, then each task's `wpt_errorsummary.log` and `wpt_instruments.txt`.

## Question: which tests ran in a WPT job, and their outcomes

- Command: `fx-tests task GbrGCuaCS3iENcSGADq-6Q --profiles`
- Expected: the per-test table; the resource-usage profile has 98 `test` markers, e.g. `TIMEOUT — /core-aam/aamtests/attribute/aria_autocomplete_inline_list_both.py`.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", plus a warning that 2 failing markers "named no test path". The job ran ~100 tests.
- Workaround: loaded the resource-usage profile in profiler-cli and listed `name:test` markers.
