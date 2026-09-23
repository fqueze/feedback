## What do all the minidumps of this test's crash have in common?

- Command: `fx-tests test <path> --task-ids --limit 0` (to get 40 task/dump pairs), then a shell loop of `fx-tests crash <task> <dump> --frames 0 --all-threads` over all 40, grepping for `nsAppShell::Init`, `CreateHeadless`, `_LSFindOrRegisterBundleNode`, the executable name.
- Question: "which process hung, and where, in each of the 39 `child process hang at shutdown` dumps of this test?"
- What its output could have shown: a per-signature summary across the dumps of one test (process type, the frame each thread is parked in, most common non-system frames), e.g. `fx-tests test <path> --issue 2 --dumps-summary`.

## `fx-tests crash` does not say which process the dump is from

- Command: `fx-tests crash M9OTueFTRSe2KMDtQ97YNw.0 BFB0A47A-CE2C-4303-9765-54BD2742D078`
- Expected: the process type (the `.extra` has `ProcessType`) in the header — for `child process hang at shutdown` the dump is always of a child, and which child is the first question.
- Got: only discoverable from a frame, `Firefox Nightly GPU Helper + 0x00000000000006b5`, near the bottom of the main thread.

## `fx-tests failures --message` on a crash signature says "No failure matched"

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes`.
- Got: "No failure matched. Searched 4,986 tests …", although `fx-tests test` lists it as the test's top issue and `fx-tests crashes` has it at 19,591 crashes in 507 tests. It reads as "this message does not exist".
