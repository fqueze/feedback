## Question: "on which machine did each occurrence run?"

- Command: `fx-tests intermittent --bug 2048385` (default output)
- Expected: the Occurrences table to show the worker/machine name, or a "Machines" breakdown like "Platforms".
- Got: machine name only in `--json` (`occurrenceRows[].machineName`). All 4 annotated occurrences were on `macmini-r8-183`, which is the whole diagnosis for this bug.
- Workaround: `--json`, then for the 15 crashes from `fx-tests test <path> --task-ids` (which has no machine at all), one `curl .../api/queue/v1/task/<id>/status` per task to read `runs[].workerId`. All 15 were `macmini-r8-183` too.
- Suggestion: a "Machines" section in `intermittent`, and a worker column in `test --task-ids`, with a flag when one worker accounts for all failures.

