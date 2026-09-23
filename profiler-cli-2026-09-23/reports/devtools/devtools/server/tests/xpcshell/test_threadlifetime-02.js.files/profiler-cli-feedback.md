## Question: "how many tests timed out without their process ever launching, and from when"

- Commands: `profiler-cli thread markers --search "name:test" --list --limit 0 --json` and `--search "will retry" --list --limit 0 --json`, then a Python script joining them by test name to compute, for each TIMEOUT test started after the first launch failure, its duration and the delay to its `will retry` INFO.
- The default output could have shown it with a way to filter `test` markers by payload field and start time together (e.g. `--search status:TIMEOUT` plus a `--from <t>` bound without zooming), and a count/min/max of durations in the summary view (the aggregated view gives counts per name, not duration ranges per filtered set).
- Separate friction: `--search "TIMEOUT —" --list --json` matched 0 markers although the printed labels read `TIMEOUT — <path>`; the label is not searchable, only the fields. `--search TIMEOUT` works.
