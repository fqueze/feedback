## Question: "what did this test fail with on my try push?"

- Command: `fx-tests try <rev> --profiles --task-ids --full-messages`
- For a NEW INTERMITTENTS row (`browser_new_tab_bookmarks_toolbar_height.js`), the detail block printed configs, "passed on harness rerun", central rates, task ids and profile URLs, but not the failure message; the PERMA-FAILS rows do print it ("Test timed out").
- Could have shown: the `messages` / `allMessages` lines, as for perma-fails.
- Workaround: `--json` and read `newIntermittents[i].allMessages`.

## Question: "did test X run in job Y, and with what status?"

- Command: `fx-tests task <taskId>`
- Checking an order dependency needs to know whether a candidate leaker and a candidate cleaner ran in the same job as the victim (here: one test PASS, one SKIP by `run-if`, on each of 6 Mac jobs and one Windows job). The text output lists only failures.
- Could have shown: `fx-tests task <taskId> --test <substring>` printing the matching tests' statuses, or the job's full test list in order with `--all`.
- Workaround: `--json` and a script over `passed` + `failures`.
