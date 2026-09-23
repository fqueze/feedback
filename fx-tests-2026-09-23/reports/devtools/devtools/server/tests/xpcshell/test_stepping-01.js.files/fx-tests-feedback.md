## Where each of one test's minidumps was stuck

Question: "across the 105 `child process hang at shutdown` dumps of this test, which function was the hung process's main thread (and its Renderer thread) in?"

- Command: `fx-tests test <path> --task-ids --limit 0 --json` for the dump ids, then `fx-tests crash <task> <dump> --all-threads --frames 0` once per dump.
- Expected: a way to group a test's dumps by a frame of interest, since a hang dump's crashing-thread signature is always breakpad's own frames and says nothing.
- Got: one command per dump; the text output has to be split by thread and grepped (script `classify_dumps.py` here, 8 in parallel, a few minutes).
- What could answer it: `fx-tests test <path> --dumps --group-by-frame <regex>` (or `fx-tests crash --task-ids-from <path>`) printing, per dump, the first frame of the main thread below the signal handler, and counting them.
