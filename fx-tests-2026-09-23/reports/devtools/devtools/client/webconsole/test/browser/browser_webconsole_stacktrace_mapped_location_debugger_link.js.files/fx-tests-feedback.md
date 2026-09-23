## `fx-tests test` misses most failing jobs of one autoland push, and a whole config

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_stacktrace_mapped_location_debugger_link.js --task-ids --limit 0`
- Expected: every failing job. Push 82333bc598c6 (autoland, 2026-09-16) has 17 testfailed devtools-chrome jobs on Treeherder: a11y-checks x7, tsan/opt devtools-chrome-1 x6, windows11-64-25h2/opt devtools-chrome x4.
- Got: 6 failures in 3 jobs, linux only. `fx-tests task` on three uncounted jobs (JfZ3g5iLQSKPQEAY7FHmOg, which is Windows, plus CJoBEPdARRGJLu5AtXENnQ and YsBDSP2eRQ29JY1USd8tUA) shows the test failing in each one. Same thing for crashes: `fx-tests test netwerk/test/browser/browser_test_offline_tab.js` reports 0 crashes, but `fx-tests task VS-KPChQQj2yLEZDFd-j4A` (same push) shows 10 CRASH executions.
- Workaround: listed the push's jobs with the Treeherder API (`/api/jobs/?push_id=...`) and ran `fx-tests task` on them.

## Question: "which push made this one-day spike, and was it backed out?"

- Command: `fx-tests test <path> --history` followed by `--task-ids`
- Wanted: the revision behind each failing task, next to the task. It could also say when all failures come from a single push, and whether that push was backed out.
- Got: task IDs only, so I had to run `fx-tests task` to get the revision, then hg `json-pushes` for the push's contents and its backout.
- Also: `fx-tests try 82333bc598c6` answers "no push found for revision on try". An autoland revision would need the same "all failures in this push" view.

## Question: "what was the assertion text of this CRASH?"

- Command: `fx-tests task VS-KPChQQj2yLEZDFd-j4A --json`
- Got: `"messages": ["@ js::RegExpShared::execute"]`, with no minidump ID, so `fx-tests crash` could not be used.
- Could show: the `PROCESS-CRASH` line's reason (`MOZ_ASSERT(cx->hadUncatchableException())`). Workaround: grepped live_backing.log.
