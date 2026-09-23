## Question: which process crashed in this CRASH execution, and with what stack?

- Commands: `fx-tests task STEiuzNkS7-YEkXQUu0XNg --messages`, `fx-tests test <path> --task-ids --limit 0`, and both with `--json`.
- Expected: the minidump ID of the CRASH execution (so `fx-tests crash <task> <minidump>` works), or at least the crashing process type.
- Got: `task` shows "CRASH, TIMEOUT — 2 failing executions of 3" with the signature attached under the "Test timed out" message; `test --task-ids` lists the task only as a TIMEOUT, with no minidump ID; `crashSignatures` in `test --json` is empty.
- Workaround: downloaded `live_backing.log` and grepped `PROCESS-CRASH` / `Assertion failure` — it named the child pid (a content process) and the full stack, which showed the crash is shutdown fallout of the timeout.
