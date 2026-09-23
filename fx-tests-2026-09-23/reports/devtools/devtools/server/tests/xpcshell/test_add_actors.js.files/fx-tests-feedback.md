## Question: "which process and which stack is behind this crash signature, across this test's occurrences?"

- Commands: `fx-tests test <path> --task-ids --limit 0` (118 `fx-tests crash <task> <dump>` lines), then a shell loop running `fx-tests crash … --all-threads --frames 0` on every fifth dump and grepping for `nsAppShell::Init`, `RenderThread::ShutDown`, `_LSBundleFindWithNode` and the helper process name.
- `child process hang at shutdown` is a fixed signature (the parent SIGABRTs the child), so it says nothing about which child or where it hung. Every occurrence has to be opened to learn that.
- What would have answered it: for signature-override crashes, a grouping of the dumps by process name and by a few top non-system frames of the main thread, e.g. `fx-tests test <path> --crash-stacks`.

## `crashes` has no `--tests`, while `failures` does

- Command: `fx-tests crashes --harness xpcshell --signature "child process hang" --tests`
- Expected: the tests behind the signature, as `failures --tests` gives for a message.
- Got: `fx-tests: unknown option --tests`. Also, `failures --message "child process hang at shutdown"` finds nothing, because crashes are not in the failures file.
- Workaround: `--path <dir>` to get a count per directory; `fx-tests task <id>` to list the tests in one job.

## `task --profiles` does not say when a failure has no per-test profile

- Command: `fx-tests task YzPRMR_SR0WRwpltmAcKEA.0 --profiles`
- Expected: next to each failure, its per-test profile, or "no per-test profile uploaded" (the case for these xpcshell CRASHes).
- Got: no profile line at all under the failures. Only `--json` (`"testProfiles": []`) confirmed that none existed, not just that none was printed.
