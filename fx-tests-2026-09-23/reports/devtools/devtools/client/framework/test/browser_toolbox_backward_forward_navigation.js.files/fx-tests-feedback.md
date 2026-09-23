## The bug that covers a crash signature

- Command: `fx-tests test <path> --bugs` (and `--bugs --json`, `annotatedBugs: []`)
- Expected: the bug covering this crash signature (`@ mozilla::dom::WorkerPrivate::JSAsyncTaskStarted`). Bug 2009527, "Assertion failure: ref, at .../WorkerPrivate.cpp:5046", is exactly this assertion.
- Got: the text output prints nothing about bugs at all, not even "none found", so it looks as if the flag was ignored. It also cannot find a bug that names the assertion message instead of the test or the signature.
- Workaround: Bugzilla REST searches (summary, quicksearch, cf_crash_signature all empty); I found it through the `regressions` field of bug 1988289, which added the assert.
