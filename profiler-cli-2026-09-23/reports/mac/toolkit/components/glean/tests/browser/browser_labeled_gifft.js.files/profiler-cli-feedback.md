## Which test does each "Entering test" marker belong to?

- Command: `profiler-cli thread markers --session <s> --search "Entering test,testResetFOG" --list --limit 0`
- Expected: the `Test Name` field shown in the row, e.g. `Entering test toolkit/.../browser_event_leak.js`.
- Got: `INFO  t=2.037s  instant  ✗  Entering test ` for every test. The name is only in `marker info`, so I needed a second call with 7 handles to map rows to tests.
- Workaround: `profiler-cli marker info m-20 m-22 ... | grep "Test Name"`.

## `load` selected a content-process thread

- Command: `profiler-cli load <taskcluster url of profile_browser_labeled_gifft.js.json for task fVQSJdrKTaKUPlc3ICY5mQ> --session browser_labeled_gifft.js-2`, then `thread markers --search ... --list`.
- Expected: the parent process GeckoMain (t-0) selected, as it was for the same kind of profile from another task.
- Got: `Selected thread: t-10 (GeckoMain, Privileged Content)`, so my first marker query said "No markers match the specified filters". Nothing in that output pointed to the thread being the cause.
- Workaround: `thread select t-0`. It would help if an empty result named the thread it searched, or if other threads matched.
