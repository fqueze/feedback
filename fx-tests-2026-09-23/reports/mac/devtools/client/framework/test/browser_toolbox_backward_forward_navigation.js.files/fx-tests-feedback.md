## Question: the bug behind this crash signature

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_backward_forward_navigation.js --bugs`
- Expected: any bug about the failure, including one filed on the crash signature / assertion message rather than on the test path.
- Got: no bug section at all (not even "no bug names this test"), for a test whose only failure mode is `CRASH @ mozilla::dom::WorkerPrivate::JSAsyncTaskStarted`. Bug 2009527 ("Assertion failure: ref, at …/dom/workers/WorkerPrivate.cpp:5046", same stack) exists, but names neither the test nor has a `cf_crash_signature`.
- Workaround: Bugzilla REST searches by crash signature and summary returned nothing; found bug 2009527 through the `regressions` field of the bug that added the assertion (bug 1988289, from `git log -S`).
- What would have answered it: an explicit "no bug names this test" line under `--bugs`, and a search on the assertion text (`Assertion failure: ref, at` + file name, without the line number that drifts) or the crash signature's function name in bug summaries.
