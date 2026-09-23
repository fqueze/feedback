# fx-tests feedback (browser_dbg-breakpoints-columns.js)

## Question: how many other tests fail with the same message tree-wide?

- Command: `fx-tests failures --harness mochitest --message "application timed out"` (also tried `--message "Test exceeded time limit"`, the text `fx-tests test` prints under Issues for this test's TIMEOUT)
- Expected: the tests whose failing runs carry that message.
- Got: "No failure matched" for both, while `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-breakpoints-columns.js` lists `1x TIMEOUT Test exceeded time limit` under Issues and the errorsummary's message is `application timed out after 370.0 seconds with no output`.
- Workaround: none; left the tree-wide prevalence of this hang unmeasured.

## Question: which failing task has a harness hang profile?

- Command: `fx-tests task GPDkppw1Q_iVgh_0OWbXIw --profiles`
- Expected: the `public/test_info/profile_0_8231.json` artifact listed, since it is the only per-process profile of the hung browser.
- Got: "No failing test named a per-test profile in this job." The profile exists (written by the harness's SIGUSR1/SIGUSR2 hang capture, named in the log as `profiler Saving profile to .../profile_0_8231.json`), but is not a per-test `profile_<name>.json.gz`.
- Workaround: listed the task's artifacts with the Taskcluster API.

## Question: did the harness retry the test, and did the retry fail too?

- Command: `fx-tests task GPDkppw1Q_iVgh_0OWbXIw --profiles`
- Expected: one execution (the job is `-standalone`, the log has a single `TEST-START` for the test), with status TIMEOUT and the harness-kill CRASH attached to it.
- Got: `CRASH, TIMEOUT — 2 failing executions of 2`, which reads as a failed retry.
- Workaround: counted `TEST-START` lines in `live_backing.log`.
