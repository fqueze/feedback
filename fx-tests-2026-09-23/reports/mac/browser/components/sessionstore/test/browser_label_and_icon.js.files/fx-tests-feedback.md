# fx-tests feedback

## Question: "the task IDs of the runs that failed then passed on retry, for one test on a try push"

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/components/sessionstore/test/browser_label_and_icon.js --task-ids --profiles` (also `--json`)
- Expected: the task ID (and per-test profile URL) behind each "passed on retry" count.
- Got: only the per-config counts table (`opt-mochitest-browser-chrome-12: 1 passed on retry`); `--task-ids`/`--profiles` silently ignored, and the JSON has no task IDs either. The default `fx-tests try <rev> --profiles --task-ids` only listed the no-nv-8 failure, since the chrome-12 job was green.
- Workaround: Treeherder API (`/api/jobs/?push_id=...`) to list the two chrome-12 tasks, then `fx-tests task` on each.
- Could have shown: the task ID per job run in the `--test` table.

## Question: "the per-test profile of a failure in a green job"

- Command: `fx-tests task KUtuXmw3QX-zY_b-V9HLFQ --profiles`
- Expected: the `profile_browser_label_and_icon.js.json` URL under the failing test, as for `fOTzvORkRA-MPynlbLtJUQ`.
- Got: the test listed as FAIL with no profile line, although the artifact `public/test_info/profile_browser_label_and_icon.js.json` exists on that task.
- Workaround: listed the task's artifacts with the Taskcluster API.

## Question: "which bug tracks this test's failures"

- Command: `fx-tests test browser/components/sessionstore/test/browser_label_and_icon.js --bugs`
- Expected: bug 1775605 ("Intermittent browser/components/sessionstore/test/browser_label_and_icon.js | single tracking bug"), which the manifest's `skip-if` comments also name.
- Got: the normal output with no bug section and no "no bugs found" line either, so it is unclear whether the flag did anything.
- Workaround: read the bug number from the `skip-if` comments in browser.toml and fetched it with the Bugzilla MCP tool.
