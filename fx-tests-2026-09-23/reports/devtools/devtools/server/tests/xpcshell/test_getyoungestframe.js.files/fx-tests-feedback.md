## `--bugs` says nothing when there are no bugs

- Command: `fx-tests test devtools/server/tests/xpcshell/test_getyoungestframe.js --bugs`
- Expected: a line such as `Bugs: none naming this test` after the Issues block.
- Got: the ordinary output, ending at Issues, with no bug section at all, so "no bugs" reads the same as "the flag was ignored" or "the query failed". I re-ran it and then searched Bugzilla by hand to be sure.
- Workaround: `curl` on the Bugzilla REST API.

## Question: do all the minidumps behind one failure mode show the same stack?

- Command: a shell loop running `fx-tests crash <task> <dump> --frames 0 --all-threads` over the 110 dumps listed by `fx-tests test <path> --task-ids`, grepping each for key frames (40 dumps took several minutes).
- What its output could have shown: `fx-tests test <path> --task-ids --issue <n>` (or a `--group-stacks` flag) could group the dumps by process type and the top non-breakpad frames of the crashing thread, e.g. "32 x GPU Helper main thread in nsAppShell::Init > _HIMagnifiedMode; 7 x GPU Helper main thread in RenderThread::ShutDown". With a uniform signature such as `child process hang at shutdown`, the signature says nothing, and the stacks are the only thing that tells failures apart.
