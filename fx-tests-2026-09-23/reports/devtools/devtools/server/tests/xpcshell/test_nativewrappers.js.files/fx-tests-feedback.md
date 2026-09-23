## `test --bugs` prints no bug section at all
- Command: `fx-tests test devtools/server/tests/xpcshell/test_nativewrappers.js --bugs`
- Expected: a "Bugs" section, even if it says "none name this test".
- Got: output identical to the plain `fx-tests test`, so "no bug" and "flag ignored" look the same.
- Workaround: queried the Bugzilla REST API by summary.

## Question: "is this failure message tree-wide?" — `failures --message` misses crash signatures
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the 19,591 failures / 507 tests that `fx-tests crashes` lists under that signature.
- Got: "No failure matched", with no hint that the text is a crash signature that `crashes --signature` would find.
- Workaround: `fx-tests crashes --harness xpcshell`.

## Question: "do all the minidumps of this test's failures agree (which process, which stack)?"
- Command: a shell loop over `fx-tests crash <task> <dump> --all-threads --frames 0` for every 5th of the 111 dumps from `test --task-ids`, grepping the process name and key frames.
- What would have answered it: a per-test crash summary grouping the dumps by crashing process type and top Firefox frame (e.g. "22/22 GPU Helper; 18 main thread in nsAppShell::Init, 4 in RenderThread::ShutDown").
