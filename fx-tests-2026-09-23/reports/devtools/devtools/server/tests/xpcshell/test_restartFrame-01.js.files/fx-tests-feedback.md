## Question: does any bug name this test?
- Command: `fx-tests test devtools/server/tests/xpcshell/test_restartFrame-01.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`, with no line about bugs at all, so "none found" and "flag ignored" look identical.

## Question: which state were the killed processes in, across all minidumps of one test?
- Command: a shell loop over `fx-tests crash <task> <dump> --all-threads --frames 45` for 12 of the 108 dumps listed by `fx-tests test <path> --task-ids --limit 0`, grepping the main thread for `nsAppShell::Init` vs `RenderThread::ShutDown`.
- Expected: some way to get the crashing process's name and the top N distinct main-thread frames across a test's dumps, grouped (e.g. `fx-tests test <path> --dumps --group-by main-thread`).
- Got: one dump per invocation; 12 invocations took a few minutes.
