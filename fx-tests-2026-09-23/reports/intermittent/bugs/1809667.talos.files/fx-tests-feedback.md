# fx-tests feedback (1809667.talos)

## Question: which of a meta bug's annotations are Talos jobs?

- Command: `fx-tests intermittent --bug 1809667 --limit 0 --config talos --json`
- Expected: the 26 occurrences whose job (`testSuite`) is `talos-*`; `--help` says `--config` takes "Comma-separated job-name substrings to include".
- Got: `fx-tests: bug 1809667 has 681 annotations on trunk, but none match the filter` (exit non-zero, empty stdout). The filter apparently matches the platform, not the job name.
- Workaround: `--json` without `--config`, then filtering `occurrenceRows[].testSuite` in Python.
- What the output could have shown: the talos rows, or a help text saying which field `--config` matches.

## Question: which of the annotated jobs of a max-run-time meta bug share a mechanism, and since when?

- Command: `fx-tests intermittent --bug 1809667 --limit 0 --since 30 --json`
- Expected: some way to see, per occurrence, how far the job got (last test started, silent tail before the abort).
- Got: job names, platforms, machines and task ids only; the window stops at 30 days, while this mode started on 2026-07-23/24.
- Workaround: downloaded all 126 talos logs (`classify.py`, `scan.py`), and the Treeherder `failuresbybug` API for 6 months of annotations plus the Treeherder jobs API for unannotated jobs at the limit (`thrange.py`).
- What the output could have shown: per occurrence the last `TEST-START`, the time from the last output line to the abort, and the job's maxRunTime; a history reaching past 30 days.
