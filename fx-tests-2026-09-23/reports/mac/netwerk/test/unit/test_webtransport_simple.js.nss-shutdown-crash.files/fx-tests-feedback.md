## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature ShutdownXPCOM --limit 0`
- Expected: the tests that crash with this signature, with counts (the question after "is this signature specific to my test?").
- Got: one row, `145 crashes, 5 tests, @ mozilla::ShutdownXPCOM`, and no test names.
- Workaround: `--json`, where `rows[].tests[]` has them (dom/webtransport/test/xpcshell/test_close.js 54, netwerk/test/unit/test_webtransport_simple.js 41, netwerk/test/unit/test_webtransport_stop_sending.js 21, ...).
- What the default output could show: the top few tests under each signature row, as `fx-tests test` lists task IDs under an issue.

## Question: which crash reason (MOZ_CRASH message) is behind a signature?

- Command: `fx-tests test netwerk/test/unit/test_webtransport_simple.js` (Issues list) and `fx-tests crash <task> <dump>`
- Expected: the crash reason (`MOZ_CRASH(NSS_Shutdown failed)`), since `@ mozilla::ShutdownXPCOM` covers several different MOZ_CRASHes (NSS_Shutdown failed, others).
- Got: signature only; `fx-tests crash` shows `EXC_BAD_ACCESS / KERN_INVALID_ADDRESS` and the frame, not the MOZ_CRASH message.
- Workaround: read the task's live_backing.log for `Hit MOZ_CRASH(...)`, or the `crashed process` line.
