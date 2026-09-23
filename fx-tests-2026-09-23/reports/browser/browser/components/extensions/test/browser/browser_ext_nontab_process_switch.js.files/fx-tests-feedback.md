## Question: "which test failed first in the same browser, in every job where this test failed?"

- Needed for a cascade (this test is one of ~45 in a manifest all failing the vsync check after one leaker).
- Command: 38 separate `fx-tests task <id> --messages --limit 0` calls in a shell loop over `fx-tests test <path> --task-ids --limit 0`, grepping for the suspected leaker's message.
- What could have shown it: `fx-tests test <path>` listing, per failure mode, the tests that failed earlier in the same manifest run in those jobs, with counts (here: `browser_ext_commands_execute_page_action.js` 38/38).

## Question: "were these failures on revisions pushed before the fix?"

- The `--history` dates are job run dates: 3 failures dated 2026-09-14 were on autoland pushes from 2026-08-15, which looked like a recurrence after the fix.
- Command: `hg json-pushes?changeset=<rev>` per revision from `fx-tests task`.
- What could have shown it: the push date next to the revision in `--task-ids`, or a flag on `--history` to bucket by push date.
