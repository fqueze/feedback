## `crash` does not say which process hung, nor which thread it was waiting for
- Command: `fx-tests crash LtzWWLsdQga6nv0JV6ne7Q.0 39353046-97F3-4182-AF85-30D835A7DC77`
- Question: "which child process hung at shutdown, and on what?"
- Expected: the process type in the header (the dump is from the GPU process), and, when the main thread is inside `nsThread::Shutdown` / `SpinEventLoopUntil`, a pointer to the thread being joined.
- Got: process type only visible as frame 40 (`Firefox Nightly GPU Helper`) with `--frames 0`; the "waiting on a lock: #0, #3, #5, #28, #29" hint omits the actual culprit, the busy `Renderer` thread (#9) that `RenderThread::ShutDown` was joining.
- Workaround: `--all-threads --frames 0` into a file, then read each thread's stack.

## `failures --message` finds nothing for a crash signature, with no hint to use `crashes`
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: a match, or "this is a crash signature, see `fx-tests crashes --signature`".
- Got: "No failure matched ... Check --path, --message ... for typos", while `fx-tests crashes` lists 19,349 of them.
- Workaround: `fx-tests crashes --harness xpcshell`.

## `test --bugs` prints no line when no bug names the test
- Command: `fx-tests test <path> --bugs`
- Expected: "No bug names this test" (or the bugs).
- Got: the same output as without `--bugs`; unclear whether it searched.

## Question: "are the failing jobs concentrated on a few workers?"
- Needed: worker IDs of failing vs passing tasks. `fx-tests` has none, so I scripted the Taskcluster queue API (`task/<id>/status` -> workerId, `workers/<group>/<id>` -> recentTasks) and re-ran `fx-tests task` on each.
- Result that answered it: 69 failures on 17 of 77 workers; on two of them every recent debug-xpcshell job had 34-113 shutdown-hang crashes, against 0-16 elsewhere. A `--workers` breakdown in `fx-tests test --task-ids` would have shown this directly.
