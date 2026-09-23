## `--bugs` finds no bug, though one names the test

- Command: `fx-tests test browser/base/content/test/fullscreen/browser_fullscreen_keyboard_lock.js --bugs` (the same with the new `browser-fullscreen/` path)
- Expected: bug 2032941, "Intermittent browser/base/content/test/fullscreen/browser_fullscreen_keyboard_lock.js | single tracking bug" (NEW), which names the old path exactly.
- Got: the usual output, with no bug section and no "no bugs found" line either.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_fullscreen_keyboard_lock.js'`.

## Question: "did the harness retry pass in every job with this failure mode?"

- Command: `fx-tests task <id> --profiles`, once per job for 21 jobs, grepping for the test's block.
- The answer is the order-dependency check the brief asks for. `fx-tests test <path> --task-ids --issue <n>` could show it per task, for example with a `retry passed` / `retry failed` column.
- Also: in `task --profiles`, the per-test line "Passed when the harness reran it." is left out when the job groups several tests under "All N passed when the harness reran them.", so a grep for it misses those jobs. Only "1 failing execution of 2" gave the answer consistently.
