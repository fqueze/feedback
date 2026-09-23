## `fx-tests test` has no crashtest data, while `fx-tests task` reads crashtest jobs

- Command: `fx-tests test dom/media/tests/crashtests/1920264.html`
- Expected: per-config rates and history for a crashtest, or a message saying crashtests are not covered.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." and a suggestion that the path may be misspelled. Yet `fx-tests task <crashtest taskId>` lists crashtest outcomes fine.
- Workaround: `fx-tests intermittent --bug <N> --history --since 30` for the time axis, `fx-tests task` per job.

## Question: which machines did the failing jobs run on?

- Command: `fx-tests intermittent --bug 1937556 --since 30 --limit 0`
- The text Occurrences table has no machine column; `machineName` is only in `--json` `occurrenceRows`. I had to script over the JSON to count failures per host (49 distinct macmini-m4 hosts) — the key evidence for a host-specific cause.
- Could show: a "Machines" summary block (like Platforms / Build types) with counts, or a machine column in Occurrences.

## Question: which tests failed together in each annotated job?

- Same command. "Tests named, per annotated job" gives per-test totals, not co-occurrence: that 1920264.html, 1991492_2.html and 1991494.html fail together in 220 of 292 jobs needed a script over `occurrenceRows[].lines`.
- Could show: the combinations of tests named per job, with counts.
