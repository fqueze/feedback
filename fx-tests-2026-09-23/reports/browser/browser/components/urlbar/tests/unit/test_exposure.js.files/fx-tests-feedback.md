## Question: "is this failure tied to particular workers (machines)?"

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_exposure.js --task-ids --limit 0 --issue 1`
- Expected: the worker (Treeherder `machine_name`) next to each failing task, or a per-machine breakdown: a crash that follows the worker is an infra problem, not a test one.
- Got: task IDs only. I had to pull every `test-macosx1015-64-qr/debug-xpcshell*` job from the Treeherder jobs API (1,380 jobs), join them on task ID in a script, and then run `fx-tests task` on one job per machine (78 calls) to count CRASHes. The result: all 43 failures were on 15 of 78 workers, where ~33 would be expected at random, and 17 workers have 20–150 `child process hang at shutdown` crashes in every job while 45 have none.
- Could have shown: a `machine` column in `--task-ids`, and a "failures by worker" block in `fx-tests test` when failures are concentrated on a few machines, using the same kind of verdict as the per-config one.

## Question: "are all the minidumps of this crash signature the same hang?"

- Command: `fx-tests crash <task> <dump> --all-threads` run in a loop over the 43 dumps listed by `--task-ids --issue 1`, with grep over the text.
- Expected: some way to classify many dumps at once, e.g. `fx-tests crash --task-ids-from <test> --issue 1 --summary`, grouped by the process type and the top non-system frames of the main thread.
- Got: one dump per call. The uniform "child process hang at shutdown" signature (bug 1888528) hides which child process hung and where, so without this every signature bucket needs a loop.
- Could have shown: the process name (here `Firefox Nightly GPU Helper`) and the main-thread frame under the event loop (`RenderThread::ShutDown` vs `nsAppShell::Init`), grouped with counts.
