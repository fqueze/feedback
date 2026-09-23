## Which execution (first run or retry) failed with which message, for one failure mode

- Question: "for the jobs behind issue N, was it the first run or the harness retry that hit it, and what did the other execution fail with?"
- Command: `fx-tests test <path> --task-ids --issue 4 --limit 0` then one `fx-tests task <id> --profiles` per task.
- Got: task IDs only. It took eight `fx-tests task` calls to see the pattern: first run `sw-error`, retry (`-2` profile) the BFCache timeout.
- Could show, per task in `--task-ids --issue`, "fails in: first run / retry / both" and the other execution's first message.

## Per-config breakdown of one failure mode

- Question: "which configs does failure mode N hit?" (here a 262-failure mode hidden under a 7,023-failure one).
- Command: `fx-tests test <path> --task-ids --issue N --limit 0`, then awk over the job names.
- Could be `fx-tests test <path> --issue N` filtering the per-config table and the history to that mode.
