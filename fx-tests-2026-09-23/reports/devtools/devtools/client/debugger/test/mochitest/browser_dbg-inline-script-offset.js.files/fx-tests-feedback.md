## Question: which other tests or jobs had a content process die with "remote browser crashed while on"?
- Command: `fx-tests errors --day 2026-09-02 --message "remote browser" --group-by test` (also `--message "remote browser crashed while on"` on 2026-09-20).
- Expected: the tests/jobs whose log has the harness's multi-line `console.error:` / `remote browser crashed while on` / `about:blank` output.
- Got: "No markers matched", although this job's log and its resource-usage profile (`output` markers "remote browser crashed while on") have it. The message is split over continuation lines after a bare `console.error:`, which the errors file apparently does not join.
- Workaround: none; I could not tell whether content processes dying at startup on Windows ASAN hits other tests.

## Question: was there a content crash during this failing test?
- Command: `fx-tests task IjDP8JAqR1uwmIEIlgccCw --profiles`, `fx-tests test <path>`.
- Expected: some hint that the TIMEOUT coincided with a content process crash (the resource-usage profile has "remote browser crashed while on" inside the test's span).
- Got: `TIMEOUT` / `Test timed out` only; `0 crash`, since no minidump was written (crash reporter off on this ASAN job).
- Workaround: loading both profiles. Surfacing the `remote browser crashed` output inside a failing test's span would have pointed straight at the cause.
