## `task` hides a fatal-condition failure when the test's own test_end says PASS

- Command: `fx-tests task ITGa2xW4Tm-I95lW9YpFsQ --profiles` (and the same for UaSzFT-sQaaEv3q7yW-G8A)
- Expected: `browser/components/urlbar/tests/browser-newtab/browser_selection.js` listed as failing. The log has `TEST-FAIL | ...browser_selection.js | fatal condition - Attempting to connect to non-local address! ...` and `profile uploaded in profile_browser_selection.js.json`, and the browser exited right after.
- Got: only other tests' failures. The `--json` output records browser_selection.js as `"statuses": ["PASS"], "executionCount": 2`. While the dump-and-quit handler spun the event loop, the test logged its own `TEST-PASS | ... | took 3048ms` before the profile was saved, and that PASS is what `task` kept. For KdpvmWnrTqa1S7nRCHXhvg, where the test_end came as FAIL after the upload, `task` does list it.
- Also missing: the job's actual failing line, `TEST-UNEXPECTED-FAIL | ShutdownLeaks | process() called before end of test suite`. `task` shows no harness-level failures, so it cannot say why a job with every test passing after retry is orange.
- Workaround: read the live log, and Treeherder's `text_log_errors`.

## Question: "what else failed in each job annotated on a generic harness-line bug"

- Command: `fx-tests intermittent --bug 1534590 --since 21 --json`, then a script fetching `https://treeherder.mozilla.org/api/project/autoland/jobs/<jobId>/text_log_errors/` for each of the 18 jobIds.
- What would have answered it: `intermittent --bug` printing each occurrence's first error line from the job's error summary, not only the lines annotated on the bug. For a catch-all bug like this one (ShutdownLeaks, "only use for classification if there is no test failure directly mentioned after it"), the precipitating crash, timeout or fatal condition is the whole diagnosis. The annotated lines alone do not say which one it was. They also show when a job was mis-starred: 2 of these 18 jobs have no ShutdownLeaks line at all.
