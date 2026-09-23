## Which code threw this uncaught rejection?

- Question: "uncaught rejection: Connection is not open." is one message shared by 27 tests (`fx-tests failures --message "Connection is not open"`); what tells them apart is the rejection's own stack, which the harness logs on the lines right after the TEST-FAIL line.
- Command: `fx-tests task b68lS5QYQTunOc2eenhyeA.0 --profiles` (and `fx-tests test <path> --task-ids`).
- Expected: the rejection stack (at least its first few frames), or a flag to show it.
- Got: only the one-line message. The per-test profile does not have it either: its TEST-UNEXPECTED-FAIL marker's stack is the harness's (`assertNoUncaughtRejections` in `_execute_test`), not the rejection's.
- Workaround: downloaded the 16 MB `public/logs/live_backing.log` of each task and grepped for the lines after the TEST-FAIL line (7 tasks). Showing the log lines that follow a failure message (e.g. `--messages --context N`) would have answered it.

## `failures --message` on a crash message says nothing matched

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes`, since `fx-tests test` lists it under Issues next to the FAIL messages.
- Got: "No failure matched ... Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes --harness xpcshell` (19,591 crashes in 507 tests). The not-found message could say that CRASH rows live in `crashes`.

## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test toolkit/components/nimbus/test/unit/shutdown/test_RSEL_withUpdateLock.js --bugs`
- Expected: a "Bugs" section, saying "none" when empty.
- Got: the normal `test` output with no bug section at all, so I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla REST quicksearch, and `fx-tests intermittent --test <path> --since 21`, which does say "no sheriff-annotated bug names the test".
