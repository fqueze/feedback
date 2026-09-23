## `--bugs` with no bug found prints nothing to say so

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_flight_status.js --bugs`
- Expected: a "Bugs" section, reading "none found" when there is none.
- Got: the same output as without `--bugs`, no Bugs section at all, so "no bug" and "flag ignored" look the same.
- Workaround: searched Bugzilla by hand.

## Question: "which workers did the failing jobs run on" (a per-machine failure)

- Command: `fx-tests test <path> --task-ids --limit 0`, then a loop over `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>/status` for `runs[n].workerId`.
- Expected: the worker ID next to each task ID (and ideally a per-worker count, against the worker's passing runs), since a failure concentrated on a few machines is an environment problem.
- Got: task IDs only; the passing runs' tasks and workers are not reachable at all, so the clustering can only be compared with a uniform draw.
- Workaround: the loop above, plus the queue's worker `recentTasks` and `fx-tests task` on those.

## Question: "is this failure ambient in the job" (one job, many tests with one message)

- Command: `fx-tests task <taskId> --limit 0 | grep -c 'child process hang at shutdown'`
- Expected: `fx-tests task` grouping its FAILED list by message with counts, e.g. "91 × child process hang at shutdown".
- Got: one entry per test (95 entries), so the count needs grep.
- Workaround: grep.
