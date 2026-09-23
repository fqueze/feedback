## Question: "which failure modes are behind the jobs sheriffs starred on this bug, and are they the same mechanism?"

- Command: `fx-tests intermittent --bug 1756294 --limit 0`
- Expected: for a harness-level bug (`<random test> | ... OSError: No data received over socket`), the log lines around the starred message, or at least the error line each occurrence was starred on.
- Got: job names and task ids only ("Tests named, per annotated job: none: no occurrence carried a TEST-UNEXPECTED-FAIL line"). `fx-tests task <id>` then says "No test-level failure in this job ... Read the log." So every occurrence needed a full live_backing.log download (4-13 MB each, 18 jobs) and grepping.
- Workaround: shell loop downloading each log and extracting the lines before the traceback.
- What the output could have shown: the Treeherder `lines` field (the starred error line) per occurrence, and for `task` on a job with an `Automation Error`, the ~20 log lines preceding it.
- Also: `intermittent` only covers 7 days; the longer history (to date when the Windows standalone occurrences started, 2026-07-31) came from Treeherder's `failuresbybug` API directly.
