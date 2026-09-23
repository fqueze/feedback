## Which stacks do this test's crash minidumps group into?

- Command: `fx-tests test <path> --task-ids --limit 0` then one `fx-tests crash <task> <dump> --all-threads` per dump (61 dumps).
- Expected: `--task-ids` (or a `--minidumps` summary) grouping the test's dumps by process type / top frames, e.g. "42× GPU process in nsAppShell::Init, 19× in RenderThread::ShutDown".
- Got: 61 separate `fx-tests crash` commands; the crashing process's executable name (here `Firefox Nightly GPU Helper`) is not printed in the header, only visible in the frames of the `start` module.
- Workaround: a shell loop over `fx-tests crash … --all-threads --frames 60` grepping for frames.

## `fx-tests test <path> --bugs` prints nothing when there is no bug

- Command: `fx-tests test browser/components/asrouter/tests/xpcshell/test_PreonboardingSplash.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the same output as without `--bugs`; no way to tell "no bug" from "the query did not run".

## Which other tests fail with this crash signature?

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind this crash signature.
- Got: "No failure matched" — crash signatures are not messages there. `fx-tests crashes --harness xpcshell` does have it (19,349 crashes, 503 tests), but `failures` could point to `crashes` when the message matches a crash signature.
