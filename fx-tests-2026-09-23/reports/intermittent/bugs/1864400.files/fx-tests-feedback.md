# fx-tests feedback (bug 1864400)

## Question: which manifest (and, in -standalone jobs, which test) did a leakcheck failure come from?

- Command: `fx-tests task B0lePDrmTMO0EXsUarWHFw --profiles` (and the same for the other 36 jobs starred on bug 1864400)
- Expected: the leakcheck failure attributed to its scope, e.g. `leakcheck | default 920 bytes leaked (...)` for `browser/components/aiwindow/ui/test/browser/browser.toml`, with the leaked class list, and in `-standalone` jobs the single test that ran in that browser session.
- Got: `164 tests, 164 executions, 0 failing` and "No test-level failure in this job ... a leak check ... Read the log."
- Workaround: downloaded all 37 `live_backing.log` files (~450 MB) and paired each `TEST-UNEXPECTED-FAIL | leakcheck` line with the preceding `leakcheck | Processing log file ... for scope <manifest>` line and the last `TEST-START`.
- What could have shown it: the log already has the scope line next to each leakcheck result, and the harness knows the scope. A "Leak checks" section in `fx-tests task` (scope, process, bytes, classes, and the test when the session ran only one) would have answered it. The same attribution in `fx-tests intermittent --bug <N>` "Occurrences" would have shown immediately that 31 of 37 jobs are one manifest.
- Related: the job's resource-usage profile has the `leakcheck | Processing log file ... for scope` INFO markers but no marker for the `TEST-UNEXPECTED-FAIL | leakcheck` line itself, and its `test` marker for the leaking test says `PASS`. So the profile cannot show a leakcheck failure either.

## Question: how often does a manifest's leakcheck fail, compared with how often it runs?

- Command: `fx-tests failures --harness mochitest --message "MozPromiseRefcountable" --tests`
- Expected: the leakcheck failures, grouped by message and scope.
- Got: "No failure matched": leakcheck results are not in the issues data.
- Workaround: took the run counts of the manifest's last test from `fx-tests test <last test> --coverage --since 7`, and the failure counts from sheriff annotations. That gives a lower bound only.
