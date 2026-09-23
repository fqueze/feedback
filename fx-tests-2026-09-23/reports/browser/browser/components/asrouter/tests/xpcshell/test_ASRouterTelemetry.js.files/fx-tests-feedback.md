## Which stacks are behind this test's crash signature?

- Command: `fx-tests crash <task> <dump>` once per line of `fx-tests test <path> --task-ids --limit 0` (65 dumps), in a shell loop grepping the output for `nsAppShell::Init` / `RenderThread::ShutDown` and the process name.
- Expected: a way to see, for one test, how the dumps behind `child process hang at shutdown` split by process type and top meaningful frame.
- Got: one dump at a time; the signature is a fixed override, so it says nothing about the hang location.
- What would have answered it: `fx-tests test <path> --task-ids` (or `crashes --path`) grouping the dumps by crashing process name and first non-breakpad/non-libsystem frame, e.g. "50x GPU Helper @ nsAppShell::Init, 15x GPU Helper @ RenderThread::ShutDown".

## Which other tests share this crash signature?

- Command: `fx-tests failures --harness xpcshell --message "hang at shutdown" --tests`
- Expected: the tests behind that message.
- Got: `No failure matched.` The text is a crash signature, only listed by `fx-tests crashes`.
- Workaround: `fx-tests crashes --harness xpcshell` (19,349 crashes, 503 tests), but that has no `--tests` listing.
- What would have answered it: `failures --message` pointing at `crashes --signature` when the text matches a signature, and `crashes --signature <s> --tests`.

## Does any bug name this test?

- Command: `fx-tests test <path> --bugs`
- Expected: a bug list, or an explicit "no bug names this test".
- Got: the normal summary with no bug section at all, so "none found" and "flag ignored" look the same.
