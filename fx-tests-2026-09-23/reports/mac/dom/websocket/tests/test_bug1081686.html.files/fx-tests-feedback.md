## Question: a passing job of this test, on the failing config, to compare against

- Command: `fx-tests test dom/websocket/tests/test_bug1081686.html --task-ids --limit 0` (and `--profiles`, `--coverage`).
- Expected: a way to get the task ID of a recent job where the test passed on the same config (here test-macosx1500-aarch64/opt-mochitest-plain-xorig), so I could compare its resource-usage timeline with the failing ones.
- Got: task IDs are listed only for failures. `--coverage` gives configs but no tasks.
- Workaround: the Treeherder REST API (`/api/project/autoland/push/?revision=…`, then `/api/jobs/?push_id=…`) on a nearby push, filtered by hand on platform and job name, then guessing which chunk held the manifest. Something like `--passing-task-ids --config <substr> --limit N` would have answered it.

## Question: which tests print a given raw log line (ssltunnel stderr)

- Command: `fx-tests errors --message "could not open connection to the real server"`.
- Expected: the tests during which ssltunnel printed this line, to measure how widely the startup race hits.
- Got: "No markers matched". The errors file holds only categorized markers (C++ warning, JS error, console.*), not raw stdout/stderr lines from the harness's helper processes.
- Workaround: downloaded each job's `live_backing.log` and grepped it. It would help if the guide said that raw helper-process output is not covered.
