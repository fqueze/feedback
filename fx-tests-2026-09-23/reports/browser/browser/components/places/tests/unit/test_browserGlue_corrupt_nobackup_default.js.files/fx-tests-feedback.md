## `failures --message` does not find a crash signature that `crashes` lists first

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests --limit 0`
- Expected: the tests failing with this message (it is the Issues line of `fx-tests test` for this test).
- Got: "No failure matched. Searched 4,982 tests". `fx-tests crashes --harness xpcshell` then showed it as the top signature (19,349 crashes, 503 tests).
- Workaround: `fx-tests crashes`. A hint in the "No failure matched" text that crash signatures are searched by `crashes` would have saved the call.

## `--since` rejects a date

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1 --since 2026-09-18`
- Got: `--since expects a non-negative integer, got "2026-09-18"`. Minor; `--day` takes dates, `--since` takes days. Accepting a date would match what `--history` prints.
