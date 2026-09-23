## Question: "which tests share this crash signature, and on which configs?"

- Commands:
  - `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests` → `No failure matched.` The signature is a crash signature, not a message, so `failures` never sees it; nothing points to `crashes` instead.
  - `fx-tests crashes --harness xpcshell --signature "child process hang at shutdown"` → `19,349 crashes, 503 tests`, but no way to list those 503 tests (`crashes` has no `--tests`), nor the configs.
- Expected: the list of tests (with counts) behind one crash signature, like `failures --tests` gives for a message — that is what says whether a test's crash is its own or a tree-wide one.
- Workaround: `fx-tests task <taskId>` on one failing job lists the other tests crashing the same way in it (90 in one job, 57 in another), which answers "is it tree-wide" for one job only.

## Question: "on which workers did the failing runs happen?"

- To test whether the failures cluster on some long-lived machines, I needed the worker of each failing task. `fx-tests test <path> --task-ids` gives task IDs only, so I queried `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>/status` for each of the 73 tasks.
- What could have shown it: a `workerId` column in `--task-ids` (and ideally the same for passing runs, to get per-worker rates).

