## How many awsy-tp6 jobs ran and failed, per platform (AWSY / marionette harness)

- Command: `fx-tests test testing/awsy/awsy/test_memory_usage.py` (and `--task-ids`, `--history`)
- Expected: per-config pass/fail counts and the failing task IDs for this marionette-based AWSY test.
- Got: `No test path in the xpcshell and mochitest 21-day data contains "testing/awsy/awsy/test_memory_usage.py"`. `fx-tests task <id> --profiles` says `0 tests, 0 executions` with the hint "the harness died before it ran one", which is misleading: the harness ran and logged TEST-UNEXPECTED-ERROR normally. `fx-tests intermittent --bug 1802130` lists only the 10 annotated jobs, while 12 more identical failures that week went unannotated.
- Workaround: Treeherder `api/project/<repo>/jobs/?job_type_name=test-<platform>/opt-awsy-tp6&last_modified__gt=<date>` for run counts, then a grep of `live_backing.log` from each failed task for the message.
- What would have answered it: a clear "harness not covered (marionette/awsy)" message instead of "no such test", and for `task`, "this job's harness is not parsed" instead of "no tests".
