## Question: "where was this uncaught rejection created?"

- Command: `profiler-cli marker info m-16 --json` on the `TEST-UNEXPECTED-FAIL uncaught rejection: Invalid tab ID: 27` marker.
- Expected: some trace of the rejection's own stack, which PromiseTestUtils passes to `Assert.report` as the assertion stack.
- Got: only Message/Test Name/Status/Expected plus the profiler's capture stack at `assertNoUncaughtRejections`. The stack field of the structured log is not recorded in the TestStatus marker (a harness/profiler-marker limitation rather than profiler-cli's), so the profile alone cannot say which code threw.
- Workaround: grep the task's live_backing.log for the lines after TEST-FAIL.
