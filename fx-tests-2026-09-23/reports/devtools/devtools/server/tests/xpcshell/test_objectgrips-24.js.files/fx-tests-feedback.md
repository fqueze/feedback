## `task` picks a different "first message" for the same failure in one job

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --json` (and the text form), test `devtools/server/tests/xpcshell/test_objectgrips-24.js`.
- Expected: the same message as in the other five msix jobs of the same breakdown (`... | Timed out and was force-killed by the harness; the crash dump reported for this test is that force-killed process, not an actual crash.`).
- Got: `messages: ["Test timed out"]`, `allMessages: []`. That is the `test` marker's own Message field. The resource-usage profile does hold the force-killed ERROR for this test (marker 10537).
- Cost: it looked like a different failure mode, so I loaded one more 0.6 GB profile to rule that out.
- Workaround: load the profile and search the test name.
